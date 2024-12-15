<script lang="ts">
	import { categorySchema } from "$lib/schema";
	import type { Category } from "$lib/types";
	import { cn } from "$lib/utils";
    import * as Form from "$lib/components/ui/form";
    import * as Select from "$lib/components/ui/select"
	import Button from "./ui/button/button.svelte";
    import { Pencil, Loader2 } from 'lucide-svelte';
	import { toast } from "svelte-sonner";
    import { type SuperValidated, type Infer, superForm } from 'sveltekit-superforms';
    import { zodClient } from 'sveltekit-superforms/adapters';

    export let data: SuperValidated<Infer<typeof categorySchema>>
    export let categories: Category[]
    console.log(categories)
    let isEditing = false;
    function toggleEdit() {
        isEditing = !isEditing;
    }
    
    const form = superForm(data, {
        validators: zodClient(categorySchema),
        onUpdated({ form }) {
            if(form.message) {
                if(!form.valid) {
                    toast.error(form.message);
                }
                if(form.valid) {
                    toast.success(form.message);
                    toggleEdit();
                }
            }
        }
    });

    const { form: formData, enhance, delayed, submitting } = form;

    $: selectedValue = categories.find((category: Category) => category.id === $formData.category)?.name
    $: selectedCategory = $formData.category ? {
        label: selectedValue,
        value: $formData.category
    } : undefined

</script>
<div class="mt-6 border bg-muted rounded-md p-4">
    <div class="font-medium flex items center justify-between">
        Course category

        <Button on:click={toggleEdit} type="button" variant="ghost">
            {#if isEditing}
                cancel
            {:else}
                <Pencil class="size-4 mr-2" />
                Edit category
            {/if}
        </Button>
    </div>
    {#if !isEditing}
        <p class={cn('text-sm mt-2', {
            'text-muted-foreground': !data.data.category
        })}>
            { selectedValue || 'No category' }
        </p>
    {:else}
        <form action="?/updateCategory" method="POST" use:enhance class="w-full space-y-6">
            <Form.Field {form} name='category' >
                <Form.Control let:attrs>
                    <Select.Root
                        selected={selectedCategory}
                        onSelectedChange={(v) => {
                            v && ($formData.category = v.value)
                        }}
                    >
                        <Select.Trigger {...attrs} class="w-[180px]">
                            <Select.Value placeholder="Select a category" />
                        </Select.Trigger>
                        <Select.Content class="w-60">
                            {#each categories as category}
                                <Select.Item value={category.id} label={category.name}>{category.name}</Select.Item>
                            {/each}
                            <!-- <Select.Item value="light">Light</Select.Item>
                            <Select.Item value="dark">Dark</Select.Item>
                            <Select.Item value="system">System</Select.Item> -->
                        </Select.Content>
                    </Select.Root>
                    <input type="text" hidden name={attrs.name} bind:value={$formData.category} id="">
                </Form.Control>
                <Form.FieldErrors />
            </Form.Field>

            <Form.Button>
                {#if $delayed}
                    <Loader2 class="size-6 animate-spin" />
                {:else}
                    save
                {/if}
            </Form.Button>
        </form>
    {/if}
</div>