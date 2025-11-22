<script>
  import { fade } from "svelte/transition";
  import { fly } from "svelte/transition";
  import { gsap } from "gsap";
  import { SplitText } from "gsap/SplitText";
  import Icon from "@iconify/svelte";
  import CVModal from "./CVModal.svelte";

  import AOS from "aos";
  import "aos/dist/aos.css"; 
  import { onMount } from "svelte";

  // Get base path from import.meta.env
  const basePath = import.meta.env.BASE_URL;

  AOS.init();

  let skills = [
    { name: "HTML", icon: "devicon:html5" },
    { name: "CSS", icon: "devicon:css3" },
    { name: "JavaScript", icon: "logos:javascript" },
    { name: "Svelte", icon: "logos:svelte-icon" },
    { name: "Vue", icon: "logos:vue" },
    { name: "Git", icon: "logos:git-icon" },
    { name: "GitHub", icon: "logos:github-icon" },
    { name: "SQL", icon: "devicon:mysql" },
    { name: "C#", icon: "devicon:csharp" },
    { name: ".NET", icon: "devicon:dotnetcore" },
  ];

  let titleEl;
  let showCVModal = false;

  onMount(() => {
    const split = new SplitText(titleEl, { type: "chars" });

    gsap.from(split.chars, {
      opacity: 0,
      y: 10,
      stagger: 0.09,
      duration: 1,
      ease: "power3.out"
    });
  });
</script>

<main class="min-h-screenbg-gray-950 text-white mt-6">
  <div
    class="flex flex-col lg:flex-row items-center lg:items-start justify-between gap-24 lg:gap-24 px-8 lg:px-42 md:pt-20"
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
            class="hidden lg:block h-1 bg-sky-500 absolute left-0 -translate-x-full w-screen"
          ></div>
          <div data-aos="fade-right" class="h-1 w-20 bg-sky-500 relative"></div>
          <p class="text-2xl md:text-3xl text-gray-300">I'm Sajjad</p>
        </div>
        <h2 bind:this={titleEl} class="text-4xl md:text-5xl font-bold mb-8">Software Developer</h2>
      </div>

      <!-- Buttons -->
      <div class="flex flex-wrap gap-4">
        <button
          onclick={() => document.getElementById('contact')?.scrollIntoView({ behavior: 'smooth' })}
          class="px-8 py-4 bg-sky-500 hover:bg-sky-600 text-white font-semibold rounded transition-colors cursor-pointer"
        >
          Need a hand?
        </button>
        <button 
          onclick={() => showCVModal = true}
          class="px-8 py-4 border-2 border-sky-500 hover:bg-sky-500/10 text-white font-semibold rounded transition-colors cursor-pointer"
        >
          View CV
        </button>
      </div>
    </div>

    <!-- Right Content -->
    <div class="flex-1 flex justify-center lg:justify-end">
      <div class="relative overflow-visible">
        <div
          in:fade={{ duration: 500 }}
          class="absolute top-10 -left-15 w-32 h-32 border-2 border-sky-500/80 transform rotate-12 animate-spin overflow-hidden"
          style="animation-duration: 8s;"
        ></div>
        <div
          in:fade={{ duration: 500 }}
          class="absolute bottom-25 -right-15 w-32 h-32 border-2 border-sky-500/80 transform -rotate-12 animate-bounce overflow-hidden"
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
      class="flex flex-wrap gap-4 justify-around items-center opacity-90 px-24"
    >
      {#each skills as skill}
        <span
          class="tracking-wide hover:text-sky-300 transition-colors cursor-default"
          title={skill.name}
        >
          <Icon icon={skill.icon} width="32" height="32" />
        </span>
      {/each}
    </div>
  </div>

  <!-- CV Modal Component -->
  <CVModal bind:show={showCVModal} pdfPath="{basePath}Sajjad Ahmed Mohammed CV (1).pdf" />
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
