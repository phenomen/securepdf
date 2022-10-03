<script lang="ts">
	import '../app.postcss';
	import { set_pdfjs_context } from '$lib/utils/vite.js';

	set_pdfjs_context();
</script>

<slot />
