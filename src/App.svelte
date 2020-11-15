<script lang="ts">
import FluidGaussian from "./FluidGaussian.svelte";
let width:number;
let height:number;
</script>

<svelte:window bind:innerWidth={width} bind:innerHeight={height} />

<FluidGaussian 
	{width} 
	{height} 
/>
<style>
	:global(html,body){
		margin: 0;
		padding: 0;
	}
</style>