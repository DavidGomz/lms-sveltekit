<script lang="ts">
    import { page } from "$app/stores";
	import { type SuperValidated, type Infer, superForm } from 'sveltekit-superforms';
    import Button from './ui/button/button.svelte';
	import { Pencil } from 'lucide-svelte';
	import { descriptionSchema } from '$lib/schema';
	import { zodClient } from 'sveltekit-superforms/adapters';
    import { toast } from 'svelte-sonner';
	// import { enhance } from '$app/forms';
    import * as Form from "$lib/components/ui/form";
    import TextArea from "$lib/components/ui/textarea/textarea.svelte"
    import Loader from "lucide-svelte/icons/loader"
	import { cn } from "$lib/utils";

    export let data: SuperValidated<Infer<typeof descriptionSchema>>
    
    const form = superForm(data, {
        validators: zodClient(descriptionSchema),
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
    let isEditing = false;
    function toggleEdit() {
        isEditing = !isEditing;
    }


</script>

<div class="mt-6 border bg-muted rounded-md p-4">
    <div class="font-medium flex items-center justify-between">
        Course Description

        <Button on:click={toggleEdit} type="button" variant="ghost">
            {#if isEditing}
                cancel
            {:else}
                <Pencil class="size-4 mr-2" />
                Edit description
            {/if}
        </Button>
    </div>
    {#if !isEditing}
        <p class={cn("text-sm mt-2", {
            'text-muted-foreground': !data.data.description
        })}>{data.data.description || "no description"}</p>
        <!-- <p class="text-sm mt-2 break-all">
            // add initial or default description
            no description
        </p> -->
    {:else}
        <form method="POST" use:enhance class="space-y-4 mt-4" action="/teacher/courses/{$page.params.courseId}/?/updateDescription">
            <Form.Field {form} name="description">
                <Form.Control let:attrs>
                    <Form.Label>Course Description</Form.Label>
                    <TextArea {...attrs} placeholder="this course is about..." bind:value={$formData.description} />
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