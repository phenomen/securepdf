<script lang="ts">
	import '../app.css';
	import { set_pdfjs_context } from '$lib/utils/vite.js';
	set_pdfjs_context();
</script>

<slot />
