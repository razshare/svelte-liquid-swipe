# Usage

Import your `LiquidContainer` component

```html
<script>
  import { LiquidContainer } from 'svelte-liquid-swipe'
</script>

<LiquidContainer />
```

The `LiquidContainer` component exposes a root element of `position:absolute`, meaning you'll have to wrap it inside your own `relative`, `fixed`, etc element in order to manage its size and position.

Something like this

```html
<script>
  import { LiquidContainer } from 'svelte-liquid-swipe'
</script>

<div style="position:relative;width:600;height:800;overflow:hidden">
  <LiquidContainer />
</div>
```

Then add some components to your container

```html
<script>
  import { LiquidContainer } from 'svelte-liquid-swipe'
  import Page1 from 'page1.svelte'
  import Page2 from 'page2.svelte'
  import Page3 from 'page3.svelte'
  import Page4 from 'page4.svelte'

  const pages = [Page1, Page2, Page3, Page4]
</script>

<div style="position:relative;width:600;height:800;overflow:hidden">
  <LiquidContainer direction="right" components="{pages}" />
</div>
```

Result

<img src="https://razshare.dev/svelte-liquid-swipe/preview.gif" />

Make sure you set `overflow:hidden` on your wrapper for the best results.
