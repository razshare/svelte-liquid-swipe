<script>
import { onMount, tick } from 'svelte';

import { cubicOut, circOut } from 'svelte/easing';
import { spring,tweened } from "svelte/motion";

export let width = 500;
export let height = 500;
export let step = 10;
export let position;
export let expansion = 50;
export let triggerValue = 350;
export let duration = 400;

const IDLE = 0, START_SLIDING = 1, SLIDING = 2;
let status = IDLE;
let mousedown = false;
let svg;
let originalTipPosition;

let pathElement;
let slide;
let tip;
let size;

let path = "";
let mounted = true;
async function onBoundsChange(width,height){
	mounted = false;
	await tick();
	mounted = true;
}

$:onBoundsChange(width,height);

function init(e){
	position = { x: 70, y: height-200 };
	originalTipPosition = position;

	slide = tweened(0, {
		duration: duration,
		easing: cubicOut
	});

	

	svg = e;
	

	size = spring( 
		{expansion},
		{
			stiffness: 0.1,
			damping: 0.2
		}
	);

	tip = spring(
		originalTipPosition,
		{
			stiffness: 0.1,
			damping: 0.2
		}
	);
	
	requestAnimationFrame(render);
}

let deltaLeft = {
	slide: 0,
	base: 0
};


function render(){
	
	let x = $tip.x < originalTipPosition.x?originalTipPosition.x:$tip.x;
	let y = height + x - $tip.y;
	let s;

	
	let coords = `M-${step},0`;
	if(
		status < START_SLIDING
		&& x > triggerValue
	) status = START_SLIDING;

	if(status === START_SLIDING){
		status = SLIDING;
		slide.set(width);
	}

	if(status >= START_SLIDING){
		$size = {
			expansion: $size.expansion + x
		};
		deltaLeft.slide = x;
		deltaLeft.base = $slide;
	}
		
	s = $size.expansion + x * 0.5;


	// start drawing
	let nx = 0;
	let ny = height + x;
	
	for (ny=height + x; ny >= 0; ny -= step){
		nx = x/Math.pow(Math.E, (Math.pow(ny-y, 2))/(2*s*s));
		coords += ` L${(deltaLeft.base + nx).toFixed(2)},${(height+x-ny).toFixed(2)}`
	}

	coords +=` L-${step},${height + x}`;
	//finish drawing
	path = coords;
	requestAnimationFrame(render);
}
</script>

{#if mounted}
<svg
	use:init
	{width}
	{height}
	viewBox="0 0 {width} {height}"
	xmlns="http://www.w3.org/2000/svg"
	on:mousedown={(e)=>{
		if(e.target === pathElement)
			mousedown=true
	}}
	on:mouseup={()=>{
		mousedown=false;
		if(status === IDLE)
			$tip = originalTipPosition
	}}
	on:mousemove={e=>{
		//if(mousedown && !sliding)
		if(mousedown && status === IDLE)
			$tip = {
				x:e.clientX,
				y:e.clientY
			}
	}}
	>
	<path bind:this={pathElement} d={path} fill="#000" stroke="black">
		hello
	</path>
</svg>
{/if}