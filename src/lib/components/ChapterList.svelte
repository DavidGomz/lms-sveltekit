<script lang="ts">
    import { page } from '$app/stores';
    import type { Chapter } from "$lib/types"
	import { cn } from "$lib/utils";
	import { Badge, Grip, Pencil } from "lucide-svelte";
	import { dndzone } from "svelte-dnd-action";
    import { flip } from 'svelte/animate';
    export let items: Chapter[];

    const flipDurationMs = 150
    function handleConsider(e: CustomEvent<DndEvent<Chapter>>) {
        items = e.detail.items
    }

    function handleFinalize(e: CustomEvent<DndEvent<Chapter>>) {
        const { items: newItems } = e.detail
    }
</script>

<ul
    use:dndzone={{items:items, flipDurationMs}}
    on:consider={handleConsider}
    on:finalize={handleFinalize}
>
    {#each items as item (item.id)}
        <li
            animate:flip={{ duration: flipDurationMs }}
            class={cn('flex items-center gap-x-2 bg-slate-200 border-slate-200 border textslate-700 rounded-md mb-4 text-sm', {
                'bg-sky-200 border-sky-200 text-sky-700': item.isPublished
            })}
        >
        <div class={cn('px-2 py-3 border-r border-r-slate-200 hover:bg-slate-300 rounded-l-md transition', {
            'border-r-sky-200 hover:bg-sky-300': item.isPublished,
        })}>
            <Grip class="size-5" />
        </div>
        <p class="truncate">
            {item.title}
        </p>
        <div class="ml-auto pr-2 flex items-center gap-x-2">
            {#if item.isFree}
                <Badge>free</Badge>
            {/if}
            <Badge class={cn('bg-slate-500', {
                'bg-sky-700': item.isPublished,
            })}>
                {item.isPublished ? 'published' : 'draft'}
            </Badge>
            <a href="{$page.url.pathname}/chapters/{item.id}">
                <Pencil class="size-4 hover:text-sky-700" />
            </a>
        </div>
        </li>
    {/each}
</ul>