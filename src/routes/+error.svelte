<script lang="ts">
    import { page } from '$app/stores';
</script>

<div class="mx-auto max-w-page">
    <h1 class="text-2xl font-bold">Whoops!</h1>
    <p class="py-4 text-md">
        {#if $page.status === 404}
        We checked everywhere, but the thing you wanted was NotFound. Sorry!
        {:else}
        An unexpected error occurred.
        {/if}
    </p>
</div>