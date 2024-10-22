<script lang="ts">
	import Button from "$lib/components/ui/button/button.svelte";
    import * as Form from "$lib/components/ui/form";
    import Input from "$lib/components/ui/input/input.svelte";
	import { courseSchema, titleSchema } from '$lib/schema.js';
	import { superForm } from 'sveltekit-superforms';
	import { zodClient } from 'sveltekit-superforms/adapters';
    import Loader from 'lucide-svelte/icons/loader';
	// import { Toaster } from "$lib/components/ui/sonner/index.js";
    import { toast } from 'svelte-sonner';

    export let data;

    const form = superForm(data.form, {
        validators: zodClient(titleSchema),
        onUpdate({form}) {
            if(form.message) {
                if(!form.valid) {
                    toast.error(form.message);
                }
            }
        }
    })
    const { form: formData, enhance, delayed , submitting } = form;
</script>

<div class="max-w-5xl mx-auto flex md:items-center md:justify-center h-full border p-6">
    <div>
        <h1 class="text-2xl">Name Your Course</h1>
        <p>
            what would like to name your course?
        </p>
        <form action="/teacher/create" use:enhance method="POST" class="space-y-8 mt-8">
            <Form.Field {form} name="title">
                <Form.Control let:attrs>
                    <Form.Label>Title</Form.Label>
                    <Input disabled={$submitting} {...attrs} bind:value={$formData.title} />
                </Form.Control>
                <Form.Description>What would you teach in this course?</Form.Description>
                <Form.FieldErrors />
            </Form.Field>
            <div class="flex items-center gap-x-2">
                <Button variant="ghost" href="/">
                    Cancel
                </Button>
                <Form.Button>
                    {#if $delayed}
                        <Loader class="size-6 animate-spin" />
                    {:else}
                        Continue
                    {/if}
                </Form.Button>
            </div>
        </form>
    </div>
</div>