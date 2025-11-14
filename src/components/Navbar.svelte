<script>
  let isMenuOpen = false;
  
  function toggleMenu() {
    isMenuOpen = !isMenuOpen;
  }
</script>

<nav class="w-full px-8 lg:px-42 pt-10">
  <div class="flex items-center justify-between">
    <!-- name -->
    <div class="shrink-0">
      <h1 class="text-2xl font-bold text-gray-200 font-gelasio">Sajjad Ahmed</h1>
    </div>
    
    <!-- Desktop -->
    <ul class="hidden md:flex gap-8 lg:gap-12 text-lg">
      <li><a href="#home" class="text-gray-200 hover:text-sky-500 transition-colors">Home</a></li>
      <li><a href="#about" class="text-gray-200 hover:text-sky-500 transition-colors">About</a></li>
      <li><a href="#projects" class="text-gray-200 hover:text-sky-500 transition-colors">Projects</a></li>
      <li><a href="#contact" class="text-gray-200 hover:text-sky-500 transition-colors">Contact</a></li>
    </ul>
    
    <!-- Mobile -->
    <button 
      class="md:hidden text-gray-200 focus:outline-none"
      onclick={toggleMenu}
      aria-label="Toggle menu"
    >
      <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
        {#if isMenuOpen}
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
        {:else}
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16" />
        {/if}
      </svg>
    </button>
  </div>
  
  <!-- Mobile -->
  {#if isMenuOpen}
    <ul class="md:hidden mt-4 space-y-4 pb-4">
      <li><a href="#home" class="block text-gray-200 hover:text-sky-500 transition-colors" onclick={toggleMenu}>Home</a></li>
      <li><a href="#about" class="block text-gray-200 hover:text-sky-500 transition-colors" onclick={toggleMenu}>About</a></li>
      <li><a href="#projects" class="block text-gray-200 hover:text-sky-500 transition-colors" onclick={toggleMenu}>Projects</a></li>
      <li><a href="#contact" class="block text-gray-200 hover:text-sky-500 transition-colors" onclick={toggleMenu}>Contact</a></li>
    </ul>
  {/if}
</nav>