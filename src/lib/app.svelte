<script>
	import { LiquidContainer } from 'svelte-liquid-swipe';
	import Page1 from '$lib/page1.svelte';
	import Page2 from '$lib/page2.svelte';
	import Page3 from '$lib/page3.svelte';
	import Page4 from '$lib/page4.svelte';

	const pages = [Page1, Page2, Page3, Page4];
</script>

<div style="position:absolute;overflow:hidden;left:0;right:0;top:0;bottom:0">
	<LiquidContainer direction="right" components={pages} />
</div>
