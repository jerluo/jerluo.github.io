<script>
    import { onMount } from 'svelte';
    
    onMount(() => {
        window.open('/resume.pdf', '_blank');
    });
</script>

<div class="flex justify-center h-screen">
    <p>
        <a href="/resume.pdf" target="_blank" rel="noopener noreferrer">Click here</a> if you are not redirected.
    </p>
</div>