<script lang="ts">
    import { Button } from '$lib/components/ui/button';
    import * as Avatar from '$lib/components/ui/avatar';
    import LogOut from 'lucide-svelte/icons/log-out';
    
</script>

<div class="flex gap-x-2 ml-auto">
    <Button href="/" size="sm" variant="ghost">
        <LogOut />
        Exit
    </Button>

    <Avatar.Root>
        <Avatar.Image src=""></Avatar.Image>
        <Avatar.Fallback>LA</Avatar.Fallback>
    </Avatar.Root>

    <form method="post" action="/?/logout">

        <Button size='sm' type='submit'>Logout</Button>
    </form>
</div>