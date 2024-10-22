<script lang="ts">
	import { type SuperValidated, type Infer, superForm } from 'sveltekit-superforms';
    import Button from './ui/button/button.svelte';
	import { Pencil } from 'lucide-svelte';
	import { descriptionSchema } from '$lib/schema';
	import { zodClient } from 'sveltekit-superforms/adapters';
    import { toast } from 'svelte-sonner';
	import { enhance } from '$app/forms';
    import * as Form from "$lib/components/ui/form";

    export let data: SuperValidated<Infer<typeof descriptionSchema>>
    let isEditing = false;
    function toggleEdit() {
        isEditing = !isEditing;
    }
    const form = superForm(data, {
        validators: zodClient(descriptionSchema),
        onUpdated({ form }) {
            if(form.message) {
                if(!form.valid) {
                    toast.error(form.message);
                }
            }
        }
    })


</script>

<div class="mt-6 border bg-muted rounded-md p-4">
    <div class="font-medium flex items-center justify-between">
        Course Description
        <Button variant="ghost">
            {#if isEditing}
                cancel
            {:else}
                <Pencil class="size-4 mr-2" />
                Edit description
            {/if}
        </Button>
    </div>
    {#if !isEditing}
        <p class="text-sm mt-2 break-all">
            <!-- add initial or default description -->
            no description
        </p>
    {:else}
        <form method="POST" use:enhance class="space-y-4 mt-4" action="/teacher/courses/{$page.params.courseId}/?/updateDescription">
            <Form.Field {form} name="title">
                <Form.Control let:attrs>
                    <Form.Label>Title</Form.Label>
                    <Input {...attrs} placeholder='advance web development' bind:value={$formData.title} />
                    <TextArea {...attrs} placeholder="this course is about..." bind:value={$formData.description} />
                </Form.Control>
                <Form.FieldErrors />
            </Form.Field>
            <div class="flex items-center gap-x-2">
                <Form.Button>
                    {#if $delayed}
                        <Loader2 class="size-6 animate-spin" />
                    {:else}
                        save
                    {/if}
                </Form.Button>
            </div>
        </form>
    {/if}
</div>