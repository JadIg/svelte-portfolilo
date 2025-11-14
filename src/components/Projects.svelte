<script>
  import { fly } from "svelte/transition";

  import AOS from "aos";
  import "aos/dist/aos.css"; // You can also use <link> for styles
  // ..
  AOS.init();

  const projects = [
    {
      name: "Portfolio Website",
      description:
        "A personal portfolio website built with Svelte and Tailwind CSS to showcase my projects and skills.",
      technologies: ["Svelte", "Tailwind CSS", "JavaScript"],
    },
    {
      name: "Battleship CLI Game",
      description:
        "A command-line battleship game with AI opponent, grid-based gameplay, and turn-based combat mechanics.",
      technologies: ["C#", ".NET", "Console"],
    },
    {
      name: "Healthcare Patient Management System",
      description:
        "A full-stack healthcare platform for managing patient appointments, medical tests, doctor schedules, patient history, and electronic health records.",
      technologies: ["SvelteKit", "C#", ".NET", "PostgreSQL"],
    },
    {
      name: "Calculator App",
      description:
        "A calculator built with HTML, Vue.js, and Tailwind CSS featuring basic arithmetic operations and a clean interface.",
      technologies: ["HTML", "Vue.js", "Tailwind CSS"],
    },

    {
      name: "Cinema Booking CLI System",
      description:
        "A command-line cinema booking system with seat selection, ticket management, and booking confirmation features.",
      technologies: ["C#", ".NET", "Console"],
    },
    {
      name: "JavaScript Mini Games & Tools",
      description:
        "A collection of interactive games and utility tools built with vanilla JavaScript and CSS for practice and fun.",
      technologies: ["JavaScript", "HTML", "CSS"],
    },
  ];
</script>

<div class="w-full px-8 lg:px-56 pb-6" id="projects">
  <h1 class="text-center text-6xl font-bold text-white mb-12">Projects</h1>

  <div class="flex flex-col items-center mb-12 animate-pulse">
    <div class="w-3 h-3 bg-sky-400 rounded-full"></div>
    <div class="w-1 h-24 bg-sky-400"></div>
    <div class="w-3 h-3 bg-sky-400 rounded-full"></div>
  </div>

  <div
    class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-12"
    data-aos="fade-up"
  >
    {#each projects as project}
      <div
        class="bg-slate-800 rounded-lg border border-slate-700 p-6 hover:border-sky-500 transition-all"
      >
        <h3 class="text-2xl font-semibold text-white mb-4">{project.name}</h3>
        <p class="text-slate-400 mb-6">{project.description}</p>
        <div class="flex flex-wrap gap-4">
          {#each project.technologies as tech}
            <span
              class="text-sm bg-slate-700 text-slate-300 px-3 py-1 rounded-full"
              >{tech}</span
            >
          {/each}
        </div>
      </div>
    {/each}
  </div>

  <div class="flex flex-col items-center mt-12 animate-pulse">
    <div class="w-3 h-3 bg-sky-400 rounded-full"></div>
    <div class="w-1 h-24 bg-sky-400"></div>
    <div class="w-3 h-3 bg-sky-400 rounded-full"></div>
  </div>
</div>
