<script lang="ts">
	import { browser } from '$app/environment';

	import { Document, Page, preferThisHeight } from 'svelte-pdfjs';

	let filename = 'source.pdf';
	let scale = 1;
	let num = 1;
	let max_pages = 1;
	let height = 900;
	let double = false;

	$: step = double ? 2 : 1;
</script>

<div class="mx-auto p-2">
	<div class="flex justify-between gap-5 max-w-3xl pb-2 items-center  text-sm">
		<div class="flex space-x-1">
			<label for="double">В развороте</label>
			<input type="checkbox" name="double" bind:checked={double} />
		</div>

		<div class="flex space-x-1">
			<label for="page">Страница</label>
			<div class="flex space-x-1">
				<input
					type="number"
					name="page"
					bind:value={num}
					{step}
					min={step}
					max={max_pages}
					class="text-center rounded"
				/>
				<div>из {max_pages}</div>
			</div>
		</div>

		<div class="flex space-x-1">
			<label for="zoom">Масштаб</label>
			<button class={height == 800 ? 'bg-slate-200' : ''} on:click={() => (height = 800)}
				>50%</button
			>
			<button class={height == 900 ? 'bg-slate-200' : ''} on:click={() => (height = 900)}
				>75%</button
			>
			<button class={height == 1024 ? 'bg-slate-200' : ''} on:click={() => (height = 1024)}
				>100%</button
			>
			<button class={height == 1600 ? 'bg-slate-200' : ''} on:click={() => (height = 1600)}
				>150%</button
			>
			<button class={height == 2048 ? 'bg-slate-200' : ''} on:click={() => (height = 2048)}
				>200%</button
			>
		</div>
	</div>

	<div>
		{#if browser}
			<Document
				file="/pdf/{filename}"
				on:loadsuccess={(e) => {
					max_pages = e.detail.numPages;
					num = Math.min(num, max_pages);
				}}
				on:loaderror={(e) => console.log(e.detail + '')}
			>
				<div class="flex">
					{#if num !== 1}
						<div on:click={() => (num = num - step)} class="flex h-[{height}] align-middle w-10">
							<button>&larr</button>
						</div>
					{/if}
					<Page {scale} {num} getViewport={preferThisHeight(height)} />
					{#if double && num !== 1 && num !== max_pages}
						<Page {scale} num={num + 1} getViewport={preferThisHeight(height)} />
					{/if}
					{#if num !== max_pages}
						<div on:click={() => (num = num + step)} class="flex h-[{height}] align-middle w-10">
							<button>&rarr</button>
						</div>
					{/if}
				</div>
			</Document>
		{/if}
	</div>
</div>

<style>
	label {
		@apply font-semibold;
	}

	input {
		@apply border text-black font-semibold;
	}

	button {
		@apply border hover:bg-slate-100 rounded w-12 text-center text-sm;
	}
</style>
