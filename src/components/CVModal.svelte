<script>
  import * as pdfjsLib from "pdfjs-dist";
  import { onMount, tick } from "svelte";

  export let show = false;
  export let pdfPath = "";

  let pdfCanvas;
  let currentPage = 1;
  let totalPages = 0;
  let pdfDoc = null;
  let isLoading = false;
  let error = null;
  let hasLoaded = false;
  let zoom = 1; // user-controlled zoom (1 = 100%)
  let fitMode = 'page'; // 'page' = fit full page height+width, 'width' = fit width only

  // Set worker path for PDF.js - use unpkg as CDN
  pdfjsLib.GlobalWorkerOptions.workerSrc = `https://unpkg.com/pdfjs-dist@${pdfjsLib.version}/build/pdf.worker.min.mjs`;

  onMount(() => {
    console.log("CVModal mounted, canvas:", pdfCanvas);
  });

  async function loadPDF() {
    console.log("loadPDF called with path:", pdfPath);
    console.log("Canvas element at load:", pdfCanvas);
    if (!pdfCanvas) {
      console.error("Canvas not available! abort loadPDF");
      return;
    }
    isLoading = true;
    error = null;
    hasLoaded = true;
    try {
      console.log("Starting PDF load...");
      const loadingTask = pdfjsLib.getDocument(pdfPath);
      pdfDoc = await loadingTask.promise;
      console.log("PDF loaded, pages:", pdfDoc.numPages);
      totalPages = pdfDoc.numPages;
    } catch (err) {
      console.error("Error loading PDF:", err);
      error = "Failed to load PDF. Please try downloading instead.";
    } finally {
      isLoading = false; // ensure canvas visible before render
    }
    if (pdfDoc) {
      await renderPage(1);
      console.log("First page rendered");
    }
  }

  async function renderPage(pageNum) {
    if (!pdfDoc || !pdfCanvas) {
      console.log("renderPage called but missing:", { pdfDoc, pdfCanvas });
      return;
    }
    
    try {
      const page = await pdfDoc.getPage(pageNum);
      // Determine dynamic scale based on available width
      const baseViewport = page.getViewport({ scale: 1 });
      const containerEl = pdfCanvas.parentElement;
      const containerWidth = containerEl?.clientWidth || baseViewport.width;
      const containerHeight = containerEl?.clientHeight || baseViewport.height;
      const widthRatio = containerWidth / baseViewport.width;
      const heightRatio = containerHeight / baseViewport.height;
      let autoScale = fitMode === 'page' ? Math.min(widthRatio, heightRatio) : widthRatio;
      let scale = autoScale * zoom;
      // Device pixel ratio for crispness
      const dpr = window.devicePixelRatio || 1;
      // Effective scale capped to avoid memory blowups
      const effectiveScale = Math.min(scale, 2) * Math.min(dpr, 2);
      const viewport = page.getViewport({ scale: effectiveScale });
      
      console.log("Viewport dimensions:", viewport.width, "x", viewport.height);
      
      const context = pdfCanvas.getContext("2d");
      // Set intrinsic size accounting for DPR separately for crisp rendering
      pdfCanvas.width = viewport.width * dpr;
      pdfCanvas.height = viewport.height * dpr;
      // CSS size at logical pixels (so layout doesn't explode)
      pdfCanvas.style.width = viewport.width + 'px';
      pdfCanvas.style.height = viewport.height + 'px';
      // Scale context for DPR
      context.setTransform(dpr, 0, 0, dpr, 0, 0);
      
      console.log("Canvas dimensions set to:", pdfCanvas.width, "x", pdfCanvas.height);
      
      await page.render({
        canvasContext: context,
        viewport: viewport
      }).promise;
      
      console.log("Page render complete");
      
      currentPage = pageNum;
    } catch (error) {
      console.error("Error rendering page:", error);
    }
  }

  function nextPage() {
    if (currentPage < totalPages) {
      renderPage(currentPage + 1);
    }
  }

  function prevPage() {
    if (currentPage > 1) {
      renderPage(currentPage - 1);
    }
  }

  function close() {
    show = false;
    pdfDoc = null;
    totalPages = 0;
    currentPage = 1;
    hasLoaded = false;
    zoom = 1;
  }

  function zoomIn() {
    zoom = Math.min(zoom + 0.1, 2); // cap logical zoom
    renderPage(currentPage);
  }
  function zoomOut() {
    zoom = Math.max(zoom - 0.1, 0.5); // min zoom
    renderPage(currentPage);
  }
  function resetZoom() {
    zoom = 1;
    renderPage(currentPage);
  }

  function setFit(mode) {
    fitMode = mode;
    zoom = 1; // reset zoom when changing fit mode
    renderPage(currentPage);
  }

  // Single reactive block to handle opening
  $: if (show && pdfPath && !hasLoaded) {
    (async () => {
      // wait for modal + canvas to be in DOM
      await tick();
      if (!pdfCanvas) {
        console.log("tick done but canvas missing; waiting another tick");
        await tick();
      }
      if (pdfCanvas && !pdfDoc) {
        console.log("Triggering initial loadPDF after tick");
        loadPDF();
      }
    })();
  }
</script>

{#if show}
  <div 
    class="fixed inset-0 z-50 flex items-center justify-center bg-black/80 backdrop-blur-sm p-4" 
    role="button" 
    tabindex="0" 
    onclick={close} 
    onkeydown={(e) => e.key === 'Escape' && close()}
  >
    <div 
      class="relative w-full max-w-4xl h-[90vh] rounded-xl border border-sky-500/30 bg-gray-900 shadow-2xl overflow-hidden flex flex-col" 
      role="dialog" 
      tabindex="-1" 
      onclick={(e) => e.stopPropagation()} 
      onkeydown={() => {}}
    >
      <!-- Header -->
      <div class="flex justify-between items-center p-4 border-b border-sky-500/30 bg-gray-800/50">
        <div class="flex items-center gap-4">
          <h3 class="text-xl font-semibold text-gray-200">My CV</h3>
          {#if totalPages > 0}
            <span class="text-sm text-gray-400">
              Page {currentPage} of {totalPages}
            </span>
          {/if}
          <!-- Zoom indicator -->
          {#if pdfDoc}
            <span class="text-xs text-gray-400 ml-2">Zoom: {(zoom * 100).toFixed(0)}%</span>
          {/if}
        </div>
        <div class="flex gap-2">
          {#if pdfDoc}
            <div class="flex items-center gap-1 mr-2">
              <button onclick={() => setFit('page')} class="px-2 py-1 rounded text-xs bg-gray-700 hover:bg-gray-600 text-white border border-transparent {fitMode==='page' ? 'ring-1 ring-sky-500' : ''}" aria-label="Fit entire page">Fit Page</button>
            </div>
          {/if}
          <a 
            href={pdfPath}
            download
            class="px-4 py-2 bg-sky-500 hover:bg-sky-600 text-white font-semibold rounded transition-colors text-sm"
          >
            Download PDF
          </a>
          <button 
            class="text-2xl text-gray-400 hover:text-white bg-gray-800/80 rounded-full w-10 h-10 flex items-center justify-center" 
            onclick={close}
            aria-label="Close CV modal"
          >
            ×
          </button>
        </div>
      </div>
      
      <!-- PDF Canvas (always present) -->
      <div class="relative flex-1 overflow-auto bg-gray-800 flex items-start justify-center p-4">
        <canvas bind:this={pdfCanvas} class="shadow-2xl bg-white"></canvas>
        {#if isLoading}
          <div class="absolute inset-0 flex flex-col items-center justify-center bg-gray-800/80 text-gray-200">
            <div class="animate-spin rounded-full h-12 w-12 border-4 border-sky-500 border-t-transparent mb-4"></div>
            <p>Loading CV...</p>
          </div>
        {/if}
        {#if error}
          <div class="absolute inset-0 flex flex-col items-center justify-center bg-gray-800/90 p-6 text-center">
            <div class="text-red-400 text-lg mb-4">{error}</div>
            <a href={pdfPath} download="Sajjad_Ahmed_CV.pdf" class="px-6 py-3 bg-sky-500 text-white rounded-md hover:bg-sky-600 inline-block">Download CV</a>
          </div>
        {/if}
      </div>
      
      <!-- Navigation -->
      {#if totalPages > 1}
        <div class="flex justify-center items-center gap-4 p-4 border-t border-sky-500/30 bg-gray-800/50">
          <button 
            onclick={prevPage}
            disabled={currentPage === 1}
            class="px-4 py-2 bg-sky-500 hover:bg-sky-600 disabled:bg-gray-600 disabled:cursor-not-allowed text-white font-semibold rounded transition-colors text-sm"
          >
            Previous
          </button>
          <button 
            onclick={nextPage}
            disabled={currentPage === totalPages}
            class="px-4 py-2 bg-sky-500 hover:bg-sky-600 disabled:bg-gray-600 disabled:cursor-not-allowed text-white font-semibold rounded transition-colors text-sm"
          >
            Next
          </button>
        </div>
      {/if}
    </div>
  </div>
{/if}
