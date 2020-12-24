<script>
import FluidGaussianWrapper from "./FluidGaussianWrapper.svelte";
let width;
let height;
</script>
<svelte:window bind:innerWidth={width} bind:innerHeight={height} />
<div current-liquid-page {...$$restProps}>
    <slot name="current-page"/>
</div>
<FluidGaussianWrapper 
    position={{
        y: 600
    }}
	{width}
	{height}
	onComplete={()=>{
		console.log("completed!");
	}}
	onButtonMove={(x,y)=>{
		console.log(x,y);
	}}
>
    <div slot="page" class="wrapper-page">
        <slot name="next-page"/>
    </div>
</FluidGaussianWrapper>

<style>
    div[current-liquid-page]{
        position: fixed;
        left: 0;
        right: 0;
        top: 0;
        bottom: 0;
    }
    .wrapper-page{
        position: fixed;
        left: 0;
        right: 0;
        top: 0;
        bottom: 0;
    }
</style>