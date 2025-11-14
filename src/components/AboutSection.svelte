<script>
  import AOS from "aos";
  import "aos/dist/aos.css"; // You can also use <link> for styles
  // ..
  AOS.init();

  const services = [
    { title: "Medical practitioner" },
    { title: "Website Development" },
    { title: "Mini Apps Development" },
  ];

  const stats = [
    { value: "4", symbol: "+", label: "Completed\nProjects" },
    { value: "95", symbol: "%", label: "Client\nsatisfaction" },
    { value: "1", symbol: "+", label: "Year of\nexperience" },
  ];
</script>

<div
  class="w-full grid grid-cols-1 md:grid-cols-2 gap-18 items-center px-8 lg:px-56 py-32 md:py-48 justify-around"
  id="about"
>
  <!-- Left Side -->
  <div class="pl-8 relative" data-aos="fade-right">
    <div
      class="absolute -left-2.5 top-10 bottom-1/4 w-1 bg-sky-500 animate-pulse"
    ></div>

    {#each services as service, i}
      <div class="relative mb-12">
        <div
          class="absolute -left-12 top-6 w-4 h-4 bg-sky-500 rounded-full animate-pulse"
        ></div>

        <div class="flex items-center gap-4 group">
          <div
            class="bg-slate-800 p-4 rounded-lg border border-slate-700 transition-all group-hover:border-sky-500 text-slate-300"
          >
            <svg
              width="32"
              height="32"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
            >
              {#if i === 0}
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  width="24"
                  height="24"
                  viewBox="0 0 24 24"
                  fill="none"
                  stroke="currentColor"
                  stroke-width="1.5"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  class="lucide lucide-activity-icon lucide-activity"
                  ><path
                    d="M22 12h-2.48a2 2 0 0 0-1.93 1.46l-2.35 8.36a.25.25 0 0 1-.48 0L9.24 2.18a.25.25 0 0 0-.48 0l-2.35 8.36A2 2 0 0 1 4.49 12H2"
                  /></svg
                >
              {:else if i === 1}
                <rect x="2" y="3" width="20" height="14" rx="2" ry="2" />
                <line x1="8" y1="21" x2="16" y2="21" />
                <line x1="12" y1="17" x2="12" y2="21" />
              {:else}
                <rect x="5" y="2" width="14" height="20" rx="2" ry="2" />
                <line x1="12" y1="18" x2="12" y2="18" />
              {/if}
            </svg>
          </div>
          <h3
            class="text-xl text-slate-200 font-light transition-colors group-hover:text-sky-500"
          >
            {service.title}
          </h3>
        </div>
      </div>
    {/each}
  </div>

  <!-- Right Side -->
  <div>
    <h1 class="text-6xl font-bold text-white mb-6">About me</h1>

    <p class="text-slate-400 text-lg leading-relaxed mb-8">
      <span class="font-bold text-sky-400">From doctor to developer</span> — I
      turned my passion for systems and problem-solving into code. As
      <span class="font-bold text-slate-300"
        >a proud graduate from Iraq Tech School</span
      >, I now build clean, structured apps with modern sophisticated front and
      back-end technologies, driven by the same precision I once used in
      medicine.
    </p>

    <div class="grid grid-cols-1 md:grid-cols-3 gap-6 pt-8">
      {#each stats as stat}
        <div class="text-center">
          <div class="flex items-start justify-center mb-3">
            <span class="text-5xl font-bold text-white">{stat.value}</span>
            <span class="text-3xl font-bold text-sky-500 ml-1"
              >{stat.symbol}</span
            >
          </div>
          <p class="text-slate-400 text-sm whitespace-pre-line leading-relaxed">
            {stat.label}
          </p>
        </div>
      {/each}
    </div>
  </div>
</div>
