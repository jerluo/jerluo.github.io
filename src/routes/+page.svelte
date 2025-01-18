<script lang="ts">
    import { Mail, Linkedin, Github } from 'lucide-svelte';
    import { onMount } from 'svelte';

    let scrollContainer: HTMLDivElement | null = null;
    let topPadding: number = 0;

    onMount(() => {
        document.body.style.overflow = 'hidden';

        const handleScroll = () => {
            if (scrollContainer) {
                const scrollHeight = scrollContainer.scrollHeight;
                const containerHeight = scrollContainer.offsetHeight;

                // When the scroll reaches the bottom, jump to the top with padding
                if (scrollContainer.scrollTop >= scrollHeight - containerHeight) {
                    scrollContainer.scrollTop = containerHeight + topPadding; // Jump to the top, keeping padding
                } 
                // When the scroll reaches the top, jump to the bottom
                else if (scrollContainer.scrollTop <= 0) {
                    scrollContainer.scrollTop = scrollHeight - containerHeight - topPadding; // Jump to the bottom
                }
            }
        };

        if (scrollContainer) {
            const content = scrollContainer.querySelector('.scroll-content');
            if (content) {
                const clone = content.cloneNode(true);
                scrollContainer.appendChild(clone);
                const contentDiv = document.getElementById('content-div');
                if (contentDiv) {
                    topPadding = contentDiv.offsetHeight + 32; // 32 for image height
                }
            }

            // Attach scroll event listener
            scrollContainer.addEventListener('scroll', handleScroll);

            // Set initial scroll position to avoid jump
            scrollContainer.scrollTop = 1;
        }

        return () => {
            // Clean up
            document.body.style.overflow = 'auto'; // Restore body scroll behavior
            scrollContainer?.removeEventListener('scroll', handleScroll);
        };
    });
</script>

<div class="h-screen flex flex-col items-center">
    <!-- Infinite Scroll Container -->
    <div
        class="relative w-full max-w-2xl overflow-hidden scroll-container flex-grow mb-16 space-y-72 pt-16"
        bind:this={scrollContainer}
    >
        <div class="scroll-content space-y-72">
            {#each Array(3) as _, i}
                <div id="content-div" class="space-y-8">
                    <div class="flex items-end justify-between space-x-8">
                        <div>
                            <h1 class="text-4xl font-semibold">Jerry Luo</h1>
                        </div>
                        <div class="flex-shrink-0">
                            <img
                                src="/headshot.jpg" 
                                alt="Jerry Luo"
                                class="w-32 h-32 rounded-lg object-cover shadow-md ring-1 ring-gray-300"
                            />
                        </div>
                    </div>
                        
                    <p class="text-md text-gray-600 leading-relaxed">
                        Hi there! I'm an undergraduate computer science and data science student at the 
                        University of Wisconsin-Madison, originally from Austin, TX. In the summers of 2023
                        and 2024, I had the privilege of working at CoBank and Capital One as a software engineer intern.
                    </p>
                    <p class="text-md text-gray-600 leading-relaxed">
                        Currently, I'm the president of the UW-Madison <a href="https://madison.dssdglobal.org/">hub</a> 
                        for Data Science for Sustainable Development, a non-profit creating software to support
                        sustainable development. I am also a peer mentor for CS 620 (Computer Science Capstone).
                    </p>
                    <p class="text-md text-gray-600 leading-relaxed">
                        I love listening to new  <a href="https://open.spotify.com/user/0ftl17afr94olbxdkzbk72vyi">music</a> 
                        and collecting <a href="https://www.discogs.com/user/jerrryluo/collection">records</a>. 
                        Some of my all time favorites include Frank Ocean's <i>Blonde</i> and Radiohead's <i>In Rainbows</i>. 
                        I also love trying new restaurants. Add me on beli <a href="https://beliapp.co/app/jerluo">@jerluo</a>  
                    </p>
                </div>
            {/each}
        </div>
    </div>

    <!-- Social Links -->
    <div class="bottom-icons fixed bottom-0 left-1/2 transform -translate-x-1/2 flex justify-center space-x-8 mt-8 mb-8">
        <a
            href="mailto:jerryluoaustin@gmail.com"
            class="text-gray-600 hover:text-gray-800 transition-colors"
        >
            <Mail size={24} />
        </a>
        <a
            href="https://www.linkedin.com/in/jerrluo/"
            class="text-gray-600 hover:text-gray-800 transition-colors"
        >
            <Linkedin size={24} />
        </a>
        <a
            href="https://github.com/jerluo"
            class="text-gray-600 hover:text-gray-800 transition-colors"
        >
            <Github size={24} />
        </a>
    </div>
</div>

<style>
    .scroll-container {
        position: relative;
        overflow-y: scroll;
        -ms-overflow-style: none; /* Hide scrollbar for IE */
        scrollbar-width: none; /* Hide scrollbar for Firefox */
        overscroll-behavior: contain; /* Prevent bounce */
        flex-grow: 1; /* Allow the container to grow */
    }

    .scroll-container::-webkit-scrollbar {
        display: none; /* Hide scrollbar for WebKit */
    }

    .scroll-content > * {
        padding: 16px 0;
    }

    .bottom-icons {
        position: fixed;
        bottom: 2rem; /* Adjust as necessary for padding */
        left: 50%;
        transform: translateX(-50%);
        margin-bottom: 2rem;
    }
</style>
