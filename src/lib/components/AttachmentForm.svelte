<script lang="ts">
	import { Files, ImageIcon, Pencil, PlusCircle, UploadCloud } from "lucide-svelte";
	import Loader from 'lucide-svelte/icons/loader';
    import Button from "./ui/button/button.svelte";
	import { enhance } from "$app/forms";
	import { toast } from "svelte-sonner";
	import { invalidateAll } from "$app/navigation";
	import type { Attachment } from "$lib/types";
	import { fly } from "svelte/transition";

    export let attachments: Attachment[]
    let isEditing = false;
    let fileInput: FileList | null;
    let uploading = false;
    let deletingId: string | null = null
    let deleting: string[] = []

    function toggleEdit() {
        isEditing = !isEditing;
    }


</script>

<div class="p-4 mt-6 bg-muted rounded-md">
    <div class="font-medium flex justify-between items-center">
        Course attachments

        <Button on:click={toggleEdit} type="button" variant="ghost">
            {#if isEditing}
                cancel
            {:else if !attachments.length}
                <PlusCircle class="size-4 mr-2" />
                Add a file
            {:else}
                <Pencil class="size-4 mr-2" />
                Edit file
            {/if}
        </Button>
    </div>
    {#if isEditing}
        <form
            action="?/createAttachment"
            method="POST"
            use:enhance={() => {
                uploading = true
                return async ({ update, result }) => {
                    await update()
                    uploading = false
                    fileInput = null
                    if(result) {
                        if(result.type === 'failure') {
                            toast.error(result?.data?.message.toString() || '')
                        }
                        if(result.type === 'error') {
                            toast.error(result?.error.message.toString() || '')
                        }
                        if(result.type === 'success') {
                            toast.success(result.data.message.toString() || '')
                            toggleEdit()
                        }
                    }
                    invalidateAll()
                }
            }}
            enctype="multipart/form-data" class="">
            <div class="relative">
                <input type="file"
                class="w-full h-80 border rounded-md file:hidden text-transparent bg-transparent border-slate-300"
                bind:files={fileInput}
                name="file" id=""
                />

                <div class="absolute pointer-events-none left-1/2 top-1/2 space-y-2 text-center -translate-x-1/2 -translate-y-1/2">
                    <UploadCloud class="size-10 mx-auto" />
                    <div>
                        <p class="text-blue text-sm font-medium capitalize">
                            choose file or drag and drop
                        </p>
                        <p class="text-sm">
                            image(4mb)
                        </p>
                    </div>
                    {#if fileInput?.length === 1}
                        <button type="submit" 
                        class="px-3 py-2 text-white rounded-md bg-blue-700 capitalize font-semibold pointer-events-auto">
                            {#if uploading}
                                <Loader />
                            {:else}
                                upload {fileInput.length} file
                            {/if}
                        </button>
                    {/if}
                </div>
            </div>
            <p class="mt-4 text-muted-foreground text-xs">
                add anything your students might need to complete this course
            </p>
        </form>
    {:else if attachments.length}
        {#each attachments.filter((attachment) => !deleting.includes(attachment.id)) as attachment (attachment.id)}
            <div class="flex items-center p-3 w-full gap-x-2 bg-sky-100 border-sky-200 border text-sky-700 rounded-md mb-2" 
            in:fly={{y:20}}
            out:slide
            >
                <Files class="size-4 m-2 flex-shrink-0" />
                <p class="line-clamp-1 text-xs">
                    {attachment.name}
                </p>
                <form action="?/deleteAttachment"
                    method="POST"
                    use:enhance={() => {
                        deleting = [...deleting, attachment.id]
                        toast.success('successfully deleted course attachment')
                        return async({ update, result }) => {
                            await update()
                            deleting = deleting.filter((id) => id !== attachment.id)
                            if(result) {
                                if(result.type === 'failure') {
                                    toast.error(result?.data?.message.toString() || '')
                                }
                                if(result.type === 'error') {
                                    toast.error(result?.error.message.toString() || '')
                                }
                            }
                        }
                    }}
                    class="ml-auto"
                >
                        <input type="hidden" name="id" value={attachment.id}>
                        <button class="hover:opacity-75 transition-all">
                            <span class='size-4'>X</span>
                        </button>
                </form>
            </div>
        {/each}
    {:else}
        <div class="flex items-center justify-center h-60 bg-slate-50">
            <ImageIcon class="size-10 text-muted-foreground" />
        </div>
    {/if}

</div>