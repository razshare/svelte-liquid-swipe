<script lang="ts">
    import { LiquidContainer } from 'svelte-liquid-swipe'
    import Page1 from '../lib/components/page1.svelte';
    import Page2 from '../lib/components/page2.svelte';
    import Page3 from '../lib/components/page3.svelte';
    import Page4 from '../lib/components/page4.svelte';
    const pages = [Page1, Page2, Page3, Page4].reverse()
</script>


<div style="position:relative;width:600px;height:800px;overflow:hidden">
    <LiquidContainer direction="right" components={pages}/>
</div>