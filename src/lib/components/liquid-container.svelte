<style lang="scss">
  .current {
    z-index: 0;
    position: absolute;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
  }
  .next {
    z-index: 1;
    position: absolute;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
  }
</style>

<script>
  import GaussianSwipeFromLeft from '$lib/components/gaussian-swipe-from-left.svelte'
  import GaussianSwipeFromRight from '$lib/components/gaussian-swipe-from-right.svelte'

  /**
   * If set to `left`, the swiper will be place on the left edge of the parent.
   *
   * If set to `right`, the swiper will be place on the right edge of the parent.
   *
   * Default value is `left`.
   * @type {'left' | 'right'}
   */
  export let direction = 'left'

  /**
   * An array of components to display.
   *
   * Default value is `[]`.
   *
   * @type {Array<ConstructorOfATypedSvelteComponent>}
   */
  export let components

  /**
   * When the swiper is dragger past this value (in px), the container will transition and trigger `on:complete` at the end of the animation.
   *
   * Default value is `250`.
   */
  export let trigger = 250

  /**
   * The swiper will snap back at this position (in px).
   *
   * Default value is `70`.
   */
  export let snap = 70

  if (direction === 'right') {
    components = components.reverse()
  }

  /** @type {Array<GaussianSwipeFromLeft>} */
  let boundsLeft = []
  /** @type {Array<GaussianSwipeFromRight>} */
  let boundsRight = []

  let width = 0
  let height = 0
  let index = 0
  $: x = snap
  $: y = height * 0.7

  // $: console.log('same pages', next === current);
</script>

<!-- useful for debugging purposes -->
<!-- <div style="position:absolute; background: #000; color: #fff;padding:1rem;z-index:9">
  x:{JSON.stringify(x)}<br />
  y:{JSON.stringify(y)}<br />
  width:{JSON.stringify(width)}<br />
  height:{JSON.stringify(height)}<br />
  trigger:{JSON.stringify(trigger)}<br />
  side:{JSON.stringify(side)}<br />
</div> -->

{#each components as component, i}
  {#if (i - 1) % components.length === ((index - 1) % components.length) - 1}
    <div class="current" bind:offsetWidth={width} bind:offsetHeight={height}>
      <svelte:component this={component} />
    </div>
  {/if}
{/each}

{#if direction === 'left'}
  {#each components as component, i}
    <GaussianSwipeFromLeft
      bind:this={boundsLeft[i]}
      enabled={i % components.length === index % components.length}
      {x}
      {y}
      {width}
      {height}
      {trigger}
      on:complete
      on:buttonmove
      on:complete={() => {
        index++
        boundsLeft[index % components.length].init(x, y)
      }}
    >
      <div class="next" slot="page">
        <svelte:component this={component} />
      </div>
    </GaussianSwipeFromLeft>
  {/each}
{:else if direction === 'right'}
  {#each components as component, i}
    <GaussianSwipeFromRight
      bind:this={boundsRight[i]}
      enabled={i % components.length === index % components.length}
      {x}
      {y}
      {width}
      {height}
      {trigger}
      on:complete
      on:buttonmove
      on:complete={() => {
        boundsRight[(index + 1) % components.length].init(x, y)
        index++
      }}
    >
      <div class="next" slot="page">
        <svelte:component this={component} />
      </div>
    </GaussianSwipeFromRight>
  {/each}
{/if}
