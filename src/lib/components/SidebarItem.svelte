<script lang="ts">
	import { page } from '$app/stores';
    import { cn } from '$lib/utils';
	import type { Icon } from 'lucide-svelte';
	import type { ComponentType } from 'svelte';
    
    export let href: string;
    export let label: string;
    export let icon: ComponentType<Icon>
    
    $: pathname = $page.url.pathname;
    $: isActive = pathname === '/' && href === '/' || pathname === href || pathname.startsWith(`${href}/`);

    


</script>

<a {href} 
    class={cn('flex items-center gap-x-2 text-muted-foreground text-sm font-medium pl-6 transion-all hover:text-slate-600 hover:bg-muted', 
        {
            'tex-primary bg-primary/20 hover:bg-primary/20 hover:text-primary': isActive
        }
    )}
>
    <div class="flex items-center gap-x-2 py-4">
        <svelte:component this={icon} class={cn('text-muted-foreground', {
            'text-primary': isActive
        })}
        size={22}
        />

        {label}
    </div>

    <div class={cn('ml-auto opacity-0 border-2 border-primary h-full transition-all', {
        'opacity-100': isActive
    })}/>
</a>