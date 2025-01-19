<script>
    import { onMount } from 'svelte';
    
    onMount(() => {
        window.location.href = '/resume.pdf';
    });
</script>

<div class="flex justify-center h-screen">
    <p>
        <a href="/resume.pdf">Click here</a> if you are not redirected.
    </p>
</div>
