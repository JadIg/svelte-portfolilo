<script>
  import * as pdfjsLib from 'pdfjs-dist';
  import { onMount, tick } from 'svelte';

  export let show = false;
  export let pdfPath = '';

  let pdfCanvas;
  let modalEl;
  let pdfDoc = null;
  let currentPage = 1;
  let totalPages = 0;
  let isLoading = false;
  let error = null;
  let zoom = 1;
  let fitMode = 'page'; // 'page' or 'width'

  pdfjsLib.GlobalWorkerOptions.workerSrc = `https://unpkg.com/pdfjs-dist@${pdfjsLib.version}/build/pdf.worker.min.mjs`;

  function getHeaderHeight() {
    if (!modalEl) return 0;
    const header = modalEl.querySelector('[data-cv-header]');
    return header ? header.getBoundingClientRect().height : 0;
  }

  async function initPdf() {
    if (!pdfCanvas) return;
    isLoading = true;
    error = null;
    try {
      const task = pdfjsLib.getDocument(pdfPath);
      pdfDoc = await task.promise;
      totalPages = pdfDoc.numPages;
      currentPage = 1;
      await renderPage(currentPage);
    } catch (e) {
      console.error('PDF load error', e);
      error = 'Unable to load PDF.';
    } finally {
      isLoading = false;
    }
  }

  async function renderPage(pageNum) {
    if (!pdfDoc || !pdfCanvas) return;
    try {
      const page = await pdfDoc.getPage(pageNum);
      const baseViewport = page.getViewport({ scale: 1 });
      const container = pdfCanvas.parentElement;
      const headerH = getHeaderHeight();
      const availableWidth = (container?.clientWidth || baseViewport.width) - 8; // small padding compensation
      const availableHeight = (container?.clientHeight || baseViewport.height) - headerH - 16; // header + padding
      const widthRatio = availableWidth / baseViewport.width;
      const heightRatio = availableHeight / baseViewport.height;
      let logicalScale = fitMode === 'page' ? Math.min(widthRatio, heightRatio) : widthRatio;
      logicalScale = Math.max(0.4, Math.min(logicalScale * zoom, 2));
      const dpr = window.devicePixelRatio || 1;
      const viewport = page.getViewport({ scale: logicalScale * dpr });
      const ctx = pdfCanvas.getContext('2d');
      ctx.setTransform(1,0,0,1,0,0);
      pdfCanvas.width = viewport.width;
      pdfCanvas.height = viewport.height;
      pdfCanvas.style.width = (viewport.width / dpr) + 'px';
      pdfCanvas.style.height = (viewport.height / dpr) + 'px';
      await page.render({ canvasContext: ctx, viewport }).promise;
      currentPage = pageNum;
    } catch (e) {
      console.error('Render error', e);
    }
  }

  async function nextPage() {
    if (currentPage < totalPages) {
      await renderPage(++currentPage);
    }
  }
  async function prevPage() {
    if (currentPage > 1) {
      await renderPage(--currentPage);
    }
  }

  function zoomIn() {
    zoom = Math.min(zoom + 0.1, 2);
    renderPage(currentPage);
  }
  function zoomOut() {
    zoom = Math.max(zoom - 0.1, 0.4);
    renderPage(currentPage);
  }
  function resetZoom() {
    zoom = 1;
    renderPage(currentPage);
  }
  function setFit(mode) {
    fitMode = mode;
    zoom = 1;
    renderPage(currentPage);
  }

  function close() {
    show = false;
    pdfDoc = null;
    totalPages = 0;
    currentPage = 1;
    zoom = 1;
  }

  function handleResize() {
    if (show && pdfDoc) renderPage(currentPage);
  }

  onMount(() => {
    window.addEventListener('resize', handleResize);
    window.addEventListener('orientationchange', handleResize);
    return () => {
      window.removeEventListener('resize', handleResize);
      window.removeEventListener('orientationchange', handleResize);
    };
  });

  $: if (show && pdfPath && !pdfDoc) {
    (async () => {
      await tick();
      if (window.innerWidth < 640) fitMode = 'width';
      await initPdf();
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
      bind:this={modalEl}
      class="relative w-full max-w-4xl h-screen sm:h-[90vh] sm:max-w-4xl sm:rounded-xl rounded-none sm:border border-sky-500/30 bg-gray-900 shadow-2xl flex flex-col" 
      role="dialog" 
      tabindex="-1" 
      onclick={(e) => e.stopPropagation()} 
      onkeydown={() => {}}
    >
      <!-- Header -->
      <div data-cv-header class="flex justify-between items-center p-3 border-b border-sky-500/30 bg-gray-800/60">
        <h3 class="text-lg font-semibold text-gray-200">My CV</h3>
        {#if totalPages > 0}
          <span class="text-xs text-gray-400">Page {currentPage} of {totalPages}</span>
        {/if}
        {#if pdfDoc}
          <span class="text-xs text-gray-400">Zoom {(zoom*100).toFixed(0)}%</span>
        {/if}
      </div>
      
      <!-- PDF Canvas (always present) -->
      <div class="relative flex-1 overflow-y-auto bg-gray-800 flex items-start justify-center p-4">
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
      
      <!-- Footer Controls -->
      <div class="flex flex-wrap items-center justify-between gap-3 p-3 border-t border-sky-500/30 bg-gray-800/60 text-sm">
        <div class="flex items-center gap-2">
          <a href={pdfPath} download class="px-3 py-2 bg-sky-500 hover:bg-sky-600 text-white rounded font-medium">Download</a>
          <button onclick={() => setFit('page')} class="px-2 py-1 bg-gray-700 hover:bg-gray-600 text-white rounded {fitMode==='page' ? 'ring-1 ring-sky-500' : ''}" aria-label="Fit page">Page</button>
          <button onclick={() => setFit('width')} class="px-2 py-1 bg-gray-700 hover:bg-gray-600 text-white rounded {fitMode==='width' ? 'ring-1 ring-sky-500' : ''}" aria-label="Fit width">Width</button>
          <button onclick={zoomOut} class="px-2 py-1 bg-gray-700 hover:bg-gray-600 text-white rounded" aria-label="Zoom out">−</button>
          <button onclick={resetZoom} class="px-2 py-1 bg-gray-700 hover:bg-gray-600 text-white rounded" aria-label="Reset zoom">100%</button>
          <button onclick={zoomIn} class="px-2 py-1 bg-gray-700 hover:bg-gray-600 text-white rounded" aria-label="Zoom in">+</button>
        </div>
        <div class="flex items-center gap-2">
          <button onclick={prevPage} disabled={currentPage === 1} class="px-3 py-2 bg-sky-500 hover:bg-sky-600 disabled:bg-gray-600 disabled:cursor-not-allowed text-white rounded" aria-label="Previous page">Prev</button>
          <span class="text-gray-300">{currentPage}/{totalPages || 1}</span>
          <button onclick={nextPage} disabled={currentPage === totalPages} class="px-3 py-2 bg-sky-500 hover:bg-sky-600 disabled:bg-gray-600 disabled:cursor-not-allowed text-white rounded" aria-label="Next page">Next</button>
          <button onclick={close} class="ml-2 px-3 py-2 bg-red-600 hover:bg-red-700 text-white rounded" aria-label="Close viewer">Close</button>
        </div>
      </div>
    </div>
  </div>
{/if}
