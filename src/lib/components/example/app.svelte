<script lang="ts">
  import LiquidContainer from '$lib/components/liquid-container.svelte'
  import Page1 from '$lib/components/example/page1.svelte'
  import Page2 from '$lib/components/example/page2.svelte'
  import Page3 from '$lib/components/example/page3.svelte'
  import Page4 from '$lib/components/example/page4.svelte'

  const pages = [Page1, Page2, Page3, Page4]
</script>

<div style="position:relative;width:600px;height:800px;overflow:hidden">
  <LiquidContainer direction="right" components={pages} />
</div>
