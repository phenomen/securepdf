<script lang="ts">
	import { RenderingCancelledException } from 'pdfjs-dist';
	import type { PDFPageProxy, RenderTask } from 'pdfjs-dist';
	import type { PageViewport } from 'pdfjs-dist/types/src/display/display_utils.js';
	import { tick } from 'svelte';
	import TextLayer from './TextLayer.svelte';

	export let page: PDFPageProxy;
	export let viewport: PageViewport;
	export let render_text_layer: boolean;

	let canvas: HTMLCanvasElement;

	let render_task: RenderTask;

	async function render_page() {
		render_task?.cancel();
		await tick();
		render_task = page.render({
			canvasContext: canvas.getContext('2d')!,
			viewport
		});

		try {
			await render_task.promise;
		} catch (err) {
			if (!(err instanceof RenderingCancelledException)) throw err;
		}
	}

	$: if (viewport && canvas) render_page();
</script>

<div>
	<canvas
		bind:this={canvas}
		width={viewport?.width}
		height={viewport?.height}
	/>{#if render_text_layer}
		<TextLayer {page} {viewport} />
	{/if}
</div>

<style>
	div {
		position: relative;
		padding: 0;
	}

	canvas {
		display: block;
		margin: 0;
	}
</style>
