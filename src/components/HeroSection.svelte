<script>
  import { fade } from "svelte/transition";
  import { fly } from "svelte/transition";

  import AOS from "aos";
  import "aos/dist/aos.css"; // You can also use <link> for styles
  // ..
  AOS.init();

  let skills = [
    "Javascript",
    "Svelte",
    "Vue",
    "Git",
    "Github",
    "C#",
    ".NET",
    "SQL",
  ];
</script>

<main class="min-h-screenbg-gray-950 text-white mt-6">
  <div
    class="flex flex-col lg:flex-row items-center lg:items-start justify-between gap-12 lg:gap-24 px-8 lg:px-42 pt-20"
  >
    <!-- Left Content -->
    <div class="flex-1 space-y-8 pt-12">
      <div>
        <h1 class="text-5xl md:text-6xl lg:text-7xl font-bold mb-4">
          <div class="flex">
            <div transition:fly={{ x: -200, duration: 2000 }}>Welcome</div>
            <span class="text-sky-500 animate-bounce-once">.</span>
          </div>
        </h1>
        <div class="flex items-center gap-4 mb-8 relative">
          <div
            class="h-1 bg-sky-500 absolute left-0 w-screen -translate-x-full"
          ></div>
          <div data-aos="fade-right" class="h-1 w-20 bg-sky-500 relative"></div>
          <p class="text-2xl md:text-3xl text-gray-300">I'm Sajjad</p>
        </div>
        <h2 class="text-4xl md:text-5xl font-bold mb-8">Software Developer</h2>
      </div>

      <!-- Buttons -->
      <div class="flex flex-wrap gap-4">
        <button
          class="px-8 py-4 bg-sky-500 hover:bg-sky-600 text-white font-semibold rounded transition-colors cursor-pointer"
        >
          Need a hand?
        </button>
        <button
          class="px-8 py-4 border-2 border-sky-500 hover:bg-sky-500/10 text-white font-semibold rounded transition-colors cursor-pointer"
        >
          My CV
        </button>
      </div>
    </div>

    <!-- Right Content -->
    <div class="flex-1 flex justify-center lg:justify-end">
      <div class="relative overflow-visible">
        <div
          in:fade={{ duration: 500 }}
          class="absolute top-10 -left-15 w-32 h-32 border-2 border-sky-500/80 transform rotate-12 animate-spin"
          style="animation-duration: 8s;"
        ></div>
        <div
          in:fade={{ duration: 500 }}
          class="absolute bottom-25 -right-15 w-32 h-32 border-2 border-sky-500/80 transform -rotate-12 animate-bounce"
          style="animation-duration: 3.5s;"
        ></div>

        <!-- circle background -->
        <div
          class="absolute -inset-7 w-80 h-80 md:w-105 md:h-105 rounded-full border-8 border-sky-500/80 shadow-2xl z-0"
        ></div>

        <!-- image in front -->
        <img
          src="./Sajjad_Ahmed.png"
          alt="Sajjad"
          class="relative w-80 h-auto md:w-96 md:h-auto object-cover z-10"
        />
      </div>
    </div>
  </div>

  <!-- Skills Section -->
  <div class="py-8 bg-slate-800">
    <div
      class="flex flex-wrap gap-8 justify-around items-center opacity-40 px-24"
    >
      {#each skills as skill}
        <span
          class="text-xl md:text-2xl font-light tracking-wide hover:text-sky-300 transition-colors cursor-default"
        >
          {skill}
        </span>
      {/each}
    </div>
  </div>
</main>

<style>
  .animate-bounce-once {
    animation: bounce 0.75s ease-in-out 2;
  }

  @keyframes bounce {
    0%,
    100% {
      transform: translateY(0);
    }
    50% {
      transform: translateY(-25%);
    }
  }
</style>
