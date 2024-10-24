<script lang="ts">
	import { ImageIcon, Pencil, PlusCircle, UploadCloud } from "lucide-svelte";
	import Loader from 'lucide-svelte/icons/loader';
    import Button from "./ui/button/button.svelte";
	import { enhance } from "$app/forms";

    let isEditing = false;
    export let imageUrl: false;
    let fileInput: FileList | null;
    let uploading = false;
    function toggleEdit() {
        isEditing = !isEditing;
    }

</script>

<div class="p-4 mt-6 bg-muted rounded-md">
    <div class="font-medium flex justify-between items-center">
        Course Image

        <Button on:click={toggleEdit} type="button" variant="ghost">
            {#if isEditing}
                cancel
            {:else if !imageUrl}
                <PlusCircle class="size-4 mr-2" />
                Add an Image
            {:else}
                <Pencil class="size-4 mr-2" />
                Edit Image
            {/if}
        </Button>
    </div>
    {#if isEditing}
        <form action="?/updateImage" method="POST" use:enhance enctype="multipart/form-data" class="">
            <div class="relative">
                <input type="file"
                class="w-full h-80 border rounded-md file:hidden text-transparent bg-transparent border-slate-300"
                accept="image/*" bind:files={fileInput} name="image" id=""
                />

                <div class="absolute pointer-events-none left-1/2 top-1/2 space-y-2 text-center -translate-x-1/2 -translate-">
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
        </form>
    {:else if imageUrl}
        <img src={imageUrl} alt="" class="w-full mt-2 aspect-video object-cover"/>
    {:else}
        <div class="flex items-center justify-center h-60 bg-slate-50">
            <ImageIcon class="size-10 text-muted-foreground" />
        </div>
    {/if}

</div>