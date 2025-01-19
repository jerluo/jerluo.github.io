<script lang="ts">
    import { page } from '$app/stores';
    import '../app.css';

    $: currentPath = $page.url.pathname;
  </script>
  
  <div class="min-h-screen bg-white text-gray-800 font-inter">
    <header class="fixed top-0 left-0 right-0 p-6 bg-white/80 backdrop-blur-sm z-50">
      <nav class="max-w-2xl mx-auto flex justify-center items-center">
        <div class="space-x-4 flex items-center">
          <a 
            href="/" 
            class="hover:text-gray-600 transition-all duration-200 {currentPath === '/' ? 'text-lg font-medium' : ''}"
          >
            Home
          </a>
          <span class="text-gray-600 text-sm">•</span>
          <a 
            href="/projects" 
            class="hover:text-gray-600 transition-all duration-200 {currentPath === '/projects' ? 'text-lg font-medium' : ''}"
          >
            Projects
          </a>
          <span class="text-gray-600 text-sm">•</span>
          <a 
            href="/work" 
            class="hover:text-gray-600 transition-all duration-200 {currentPath === '/work' ? 'text-lg font-medium' : ''}"
          >
            Work
          </a>
          <span class="text-gray-600 text-sm">•</span>
          <a 
            href="/resume" 
            class="hover:text-gray-600 transition-all duration-200 {currentPath === '/resume' ? 'text-lg font-medium' : ''}"
          >
            Resume
          </a>
        </div>
      </nav>
    </header>
  
    <main class="pt-24 px-6">
      <div class="max-w-2xl mx-auto">
        <slot />
      </div>
    </main>
  </div>
  
  <style>
    :global(body) {
      margin: 0;
      font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
    }
  
    a {
      text-decoration: none;
      color: inherit;
    }
  </style>