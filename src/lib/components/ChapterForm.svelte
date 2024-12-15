<script lang="ts">
    import { page } from "$app/stores";
	import { Pencil } from "lucide-svelte";
    import Loader from 'lucide-svelte/icons/loader';
	import Button from "./ui/button/button.svelte";
	import { chapterTitleSchema } from "$lib/schema";
	import { superForm, type Infer, type SuperValidated } from "sveltekit-superforms";
	import { zodClient } from "sveltekit-superforms/adapters";
    import * as Form from "$lib/components/ui/form";
    import Input from "$lib/components/ui/input/input.svelte";
    import type { Chapter } from '$lib/types'
	import { toast } from "svelte-sonner";
    import ChapterList from './ChapterList.svelte';

    type ChapterTitleSchema = typeof chapterTitleSchema
    export let data: SuperValidated<Infer<ChapterTitleSchema>>
    const form = superForm(data, {
        validators: zodClient(chapterTitleSchema),
        // resetForm: false,
        onUpdated({ form }) {
            if(form.message) {
                if(!form.valid) {
                    toast.error(form.message);
                }
                if(form.valid) {
                    toast.success(form.message);
                    toggleCreate()
                }
            }
        }
    })
    const { form: formData, enhance, delayed, submitting } = form
    export let chapters: Chapter[]
    let isCreating = false
    function toggleCreate() {
        isCreating = !isCreating;
    }

</script>

<div class="mt-6 border bg-muted rounded-md p-4">
    <div class="font-medium flex items-center justify-between">
        Course Chapters

        <Button on:click={toggleCreate} type="button" variant="ghost">
            {#if isCreating}
                cancel
            {:else}
                <Pencil class="size-4 mr-2" />
                Add a chapter
            {/if}
        </Button>
    </div>
    {#if isCreating}
        <form action="/teacher/courses/{$page.params.courseId}/?/createChapter" method="POST" use:enhance class="space-y-4 mt-4">
            <Form.Field {form} name="title">
                <Form.Control let:attrs>
                    <Form.Label>Title</Form.Label>
                    <Input {...attrs} placeholder='e.g .. introduction to the course' bind:value={$formData.title} />
                </Form.Control>
                <Form.FieldErrors />
            </Form.Field>
            <div class="flex items-center gap-x-2">
                <Form.Button>
                    {#if $delayed}
                        <Loader class="size-6 animate-spin" />
                    {:else}
                        create
                    {/if}
                </Form.Button>
            </div>
        </form>
    {:else if chapters.length}
        <ChapterList items={chapters} />
        <p class="text-xs text-muted-foreground mt-4">Drag and drop to reorder the chapters</p>
    {:else}
        <div class="text-sm mt-2 text-slate-500 italic">
            no chapters
        </div>
        
    {/if}
</div>