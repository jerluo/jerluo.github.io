<!-- src/routes/+layout.svelte -->
<script lang="ts">
    import { Mail, Linkedin, Music, Github } from 'lucide-svelte';
    import '../app.css';
  </script>
  
  <div class="min-h-screen bg-white text-gray-800 font-inter">
    <!-- Header with added z-index -->
    <header class="fixed top-0 left-0 right-0 p-6 bg-white/80 backdrop-blur-sm z-50">
      <nav class="max-w-2xl mx-auto flex justify-center items-center">
        <div class="space-x-4 flex items-center">
          <a href="/" class="hover:text-gray-600 transition-colors">home</a>
          <span class="text-gray-300 text-sm">•</span>
          <a href="/projects" class="hover:text-gray-600 transition-colors">projects</a>
          <span class="text-gray-300 text-sm">•</span>
          <a href="/work" class="hover:text-gray-600 transition-colors">work</a>
          <span class="text-gray-300 text-sm">•</span>
          <a href="/resume" class="hover:text-gray-600 transition-colors">resume</a>
        </div>
      </nav>
    </header>
  
    <!-- Main Content -->
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