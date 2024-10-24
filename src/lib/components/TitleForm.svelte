<script lang="ts">
    import { page } from "$app/stores";
	import { Pencil } from "lucide-svelte";
    import Loader from 'lucide-svelte/icons/loader';
	import Button from "./ui/button/button.svelte";
	import { titleSchema } from "$lib/schema";
	import { superForm, type Infer, type SuperValidated } from "sveltekit-superforms";
	import { zodClient } from "sveltekit-superforms/adapters";
    import * as Form from "$lib/components/ui/form";
    import Input from "$lib/components/ui/input/input.svelte";
	import { toast } from "svelte-sonner";

    type TitleSchema = typeof titleSchema
    export let data: SuperValidated<Infer<TitleSchema>>
    const form = superForm(data, {
        validators: zodClient(titleSchema),
        // resetForm: false,
        onUpdated({ form }) {
            if(form.message) {
                if(!form.valid) {
                    toast.error(form.message);
                }
                if(form.valid) {
                    toast.success(form.message);
                    toggleEdit()
                }
            }
        }
    })
    const { form: formData, enhance, delayed, submitting } = form
    let isEditing = false
    function toggleEdit() {
        isEditing = !isEditing;

    }

</script>

<div class="mt-6 border bg-muted rounded-md p-4">
    <div class="font-medium flex items-center justify-between">
        Course Title

        <Button on:click={toggleEdit} type="button" variant="ghost">
            {#if isEditing}
                cancel
            {:else}
                <Pencil class="size-4 mr-2" />
                Edit Title
            {/if}
        </Button>
    </div>
    {#if !isEditing}
        <p class="text-sm mt-2">{data.data.title}</p>
    {:else}
        <form action="/teacher/courses/{$page.params.courseId}/?/updateTitle" method="POST" use:enhance class="space-y-4 mt-4">
            <Form.Field {form} name="title">
                <Form.Control let:attrs>
                    <Form.Label>Title</Form.Label>
                    <Input {...attrs} placeholder='advance web development' bind:value={$formData.title} />
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