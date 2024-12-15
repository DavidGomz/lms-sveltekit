<script lang="ts">
    import { page } from "$app/stores";
	import { type SuperValidated, type Infer, superForm } from 'sveltekit-superforms';
    import Button from './ui/button/button.svelte';
	import { Pencil } from 'lucide-svelte';
	import { priceSchema } from '$lib/schema';
	import { zodClient } from 'sveltekit-superforms/adapters';
    import { toast } from 'svelte-sonner';
	// import { enhance } from '$app/forms';
    import * as Form from "$lib/components/ui/form";
    import Loader from "lucide-svelte/icons/loader"
	import { cn, formatCurrency } from "$lib/utils";
	import Input from "./ui/input/input.svelte";

    export let data: SuperValidated<Infer<typeof priceSchema>>
    
    const form = superForm(data, {
        validators: zodClient(priceSchema),
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

    const { form: formData, enhance, delayed } = form;
    $: price = data.data.price
    let isEditing = false;
    function toggleEdit() {
        isEditing = !isEditing;
    }


</script>

<div class="mt-6 border bg-muted rounded-md p-4">
    <div class="font-medium flex items-center justify-between">
        Course price

        <Button on:click={toggleEdit} type="button" variant="ghost">
            {#if isEditing}
                cancel
            {:else}
                <Pencil class="size-4 mr-2" />
                Edit price
            {/if}
        </Button>
    </div>
    {#if !isEditing}
        <p class={cn("text-sm mt-2", {
            'text-muted-foreground': !data.data.price
        })}>{price && formatCurrency(price) || "no price"}</p>
        <!-- <p class="text-sm mt-2 break-all">
            // add initial or default description
            no description
        </p> -->
    {:else}
        <form method="POST" use:enhance class="space-y-4 mt-4" action="/teacher/courses/{$page.params.courseId}/?/updatePrice">
            <Form.Field {form} name="price">
                <Form.Control let:attrs>
                    <Form.Label>Price</Form.Label>
                    <Input {...attrs}
                        placeholder="set a price for your course"
                        bind:value={$formData.price}
                        type='number'
                        step='0.01'
                    />
                </Form.Control>
                <Form.FieldErrors />
            </Form.Field>
            <div class="flex items-center gap-x-2">
                <Form.Button>
                    {#if $delayed}
                        <Loader class="size-6 animate-spin" />
                    {:else}
                        save
                    {/if}
                </Form.Button>
            </div>
        </form>
    {/if}
</div>