<script lang="ts">
	import { browser } from '$app/environment';
	import { Document, Page, preferThisHeight } from 'svelte-pdfjs';
	import LightSwitch from '$lib/components/LightSwitch.svelte';

	//имя файла - поменяйте на своё
	let filename = 'source.pdf';

	//разрешить копирование текста?
	let renderTextLayer = true;

	let scale = 1;
	let num = 1;
	let max_pages = 1;
	let height = 900;
	let double = false;

	$: step = double ? 2 : 1;
</script>

<div class="flex flex-col max-w-full py-1">
	<div class="flex justify-between gap-5 pb-2 items-center text-sm mx-auto">
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
			<button class={height == 800 ? '!bg-stone-500' : ''} on:click={() => (height = 800)}
				>50%</button
			>
			<button class={height == 900 ? '!bg-stone-500' : ''} on:click={() => (height = 900)}
				>75%</button
			>
			<button class={height == 1024 ? '!bg-stone-500' : ''} on:click={() => (height = 1024)}
				>100%</button
			>
			<button class={height == 1600 ? '!bg-stone-500' : ''} on:click={() => (height = 1600)}
				>150%</button
			>
			<button class={height == 2048 ? '!bg-stone-500' : ''} on:click={() => (height = 2048)}
				>200%</button
			>
		</div>

		<LightSwitch />
	</div>

	<div class="mx-auto">
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
					<button
						disabled={num <= 1}
						on:click={() => (num = num - step)}
						class="navigation h-[{height}]"
					>
						<span>&larr</span>
					</button>

					<Page {scale} {num} {renderTextLayer} getViewport={preferThisHeight(height)} />
					{#if double && num !== 1 && num !== max_pages}
						<Page {scale} num={num + 1} {renderTextLayer} getViewport={preferThisHeight(height)} />
					{/if}

					<button
						disabled={num >= max_pages}
						on:click={() => (num = num + step)}
						class="navigation h-[{height}] "
					>
						<span>&rarr</span>
					</button>
				</div>
			</Document>
		{/if}
	</div>
</div>
