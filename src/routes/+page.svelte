<script lang="ts">
    import Icon from "@iconify/svelte";
    import { onMount } from 'svelte';

    let scrollContainer: HTMLDivElement | null = null;
    let topPadding: number = 0;
    let scrollTimeout: number | null = null;
    let contentHeight: number = 0;
    const SCROLL_THRESHOLD = 100; // Pixels from perfect center before auto-scrolling triggers
    const SCROLL_DURATION = 2000; // Animation duration in milliseconds
    const SCROLL_CENTER_TIME = 600;

    function getClosestContentSection(scrollTop: number): number {
        const sectionHeight = contentHeight + 288; // 288 is the space-y-72 class (72 * 4)
        const currentSection = Math.round(scrollTop / sectionHeight);
        return currentSection * sectionHeight;
    }

    function isCloseToCenter(currentScroll: number): boolean {
        const targetScroll = getClosestContentSection(currentScroll);
        return Math.abs(currentScroll - targetScroll) <= SCROLL_THRESHOLD;
    }

    function smoothScrollToContent() {
        if (scrollContainer) {
            const currentScroll = scrollContainer.scrollTop;
            
            // Only scroll if we're outside the threshold
            if (!isCloseToCenter(currentScroll)) {
                const targetScroll = getClosestContentSection(currentScroll);
                
                // Using scrollTo with custom duration
                const startPosition = currentScroll;
                const distance = targetScroll - startPosition;
                const startTime = performance.now();

                function animate(currentTime: number) {
                    const elapsed = currentTime - startTime;
                    const progress = Math.min(elapsed / SCROLL_DURATION, 1);
                    
                    // Easing function for smoother animation (thanks claude!)
                    const easeInOutCubic = progress < 0.5
                        ? 4 * progress * progress * progress
                        : 1 - Math.pow(-2 * progress + 2, 3) / 2;

                    if (scrollContainer) {
                        scrollContainer.scrollTop = startPosition + (distance * easeInOutCubic);

                        if (progress < 1) {
                            requestAnimationFrame(animate);
                        }
                    }
                }

                requestAnimationFrame(animate);
            }
        }
    }

    onMount(() => {
        document.body.style.overflow = 'hidden';

        const handleScroll = () => {
            if (scrollContainer) {
                const scrollHeight = scrollContainer.scrollHeight;
                const containerHeight = scrollContainer.offsetHeight;

                // Clear existing timeout
                if (scrollTimeout) {
                    window.clearTimeout(scrollTimeout);
                }

                // Set new timeout for centering
                scrollTimeout = window.setTimeout(() => {
                    smoothScrollToContent();
                }, SCROLL_CENTER_TIME);

                // Infinite scroll logic
                if (scrollContainer.scrollTop >= scrollHeight - containerHeight) {
                    scrollContainer.scrollTop = containerHeight + topPadding;
                } else if (scrollContainer.scrollTop <= 0) {
                    scrollContainer.scrollTop = scrollHeight - containerHeight - topPadding;
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
                    contentHeight = contentDiv.offsetHeight;
                    topPadding = contentHeight + 32;
                }
            }

            scrollContainer.addEventListener('scroll', handleScroll);
            scrollContainer.scrollTop = 1;
        }

        return () => {
            if (scrollTimeout) {
                window.clearTimeout(scrollTimeout);
            }
            document.body.style.overflow = 'auto';
            scrollContainer?.removeEventListener('scroll', handleScroll);
        };
    });
</script>

<div class="h-screen flex flex-col items-center">
    <div
        class="relative w-full max-w-full overflow-hidden scroll-container flex-grow mb-16 space-y-72 pt-16"
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

    <div class="bottom-icons fixed bottom-0 left-1/2 transform -translate-x-1/2 flex justify-center space-x-8 mt-8 mb-8">
        <a
            href="mailto:jerryluoaustin@gmail.com"
            class="text-gray-600 hover:text-gray-800 transition-colors"
        >
            <Icon icon="bxl:gmail" height="24"/>
        </a>
        <a
            href="https://www.linkedin.com/in/jerrluo/"
            class="text-gray-600 hover:text-gray-800 transition-colors"
        >
            <Icon icon="bxl:linkedin-square" height="24"/>
        </a>
        <a
            href="https://github.com/jerluo"
            class="text-gray-600 hover:text-gray-800 transition-colors"
        >
            <Icon icon="bxl:github" height="24"/>
        </a>
    </div>
</div>

<style>
    .scroll-container {
        position: relative;
        overflow-y: scroll;
        -ms-overflow-style: none;
        scrollbar-width: none;
        overscroll-behavior: contain;
        flex-grow: 1;
    }

    .scroll-container::-webkit-scrollbar {
        display: none;
    }

    .scroll-content > * {
        padding: 16px 0;
    }

    .bottom-icons {
        position: fixed;
        bottom: 2rem;
        left: 50%;
        transform: translateX(-50%);
        margin-bottom: 2rem;
    }
</style>