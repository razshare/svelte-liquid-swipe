<script>
import { tick } from 'svelte';

import { cubicOut } from 'svelte/easing';
import { spring,tweened } from "svelte/motion";

export let width = 500;
export let height = 500;
export let step = 10;
export let position;
export let triggerValue = 350;
export let precision = 3;
export let slideDuration = 1000;
export let slideEasing = cubicOut;
export let buttonSize = 10;
export let veilExpansionStiffness = .03;
export let veilExpansionDamping = .1;
export let buttonExpansionStiffness = .1;
export let buttonExpansionDamping = .2;


const IDLE = 0, START_SLIDING = 1, SLIDING = 2;
let status = IDLE;
let active = false;
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

function init(){
	position = { x: 70, y: height-200 };
	originalTipPosition = position;

	slide = tweened(0, {
		duration: slideDuration,
		easing: slideEasing
	});

	size = spring( 
		{buttonSize},
		{
			stiffness: veilExpansionStiffness
			,
			damping: veilExpansionDamping
		}
	);

	tip = spring(
		originalTipPosition,
		{
			stiffness: buttonExpansionStiffness
			,
			damping: buttonExpansionDamping
		}
	);
	
	requestAnimationFrame(render);
}

let deltaLeft = {
	slide: 0,
	base: 0
};

let rendering = false;
function render(){
	rendering = true;
	let x = $tip.x < originalTipPosition.x?originalTipPosition.x:$tip.x;
	let y = height + x - $tip.y;
	let s;

	
	if(
		status < START_SLIDING
		&& x > triggerValue
	) status = START_SLIDING;

	if(status === START_SLIDING){
		status = SLIDING;
		slide.set(width);
	}

	if(status >= START_SLIDING){
		size.set({
			buttonSize: $size.buttonSize + x
		});
		deltaLeft.slide = x;
		deltaLeft.base = $slide;
	}
		
	s = $size.buttonSize + x * 0.5;


	// start drawing
	let nx = 0;
	let ny = height + x;
	let coords = `M-${step},0`;
	for (ny=height + x; ny >= 0; ny -= step){
		nx = x/Math.pow(Math.E, (Math.pow(ny-y, 2))/(2*s*s));
		coords += ` L${(deltaLeft.base + nx).toFixed(precision)},${(height+x-ny).toFixed(precision)}`
	}
	coords +=` L-${step},${height + x}`;
	//finish drawing

	path = coords;
	rendering = false;
	requestAnimationFrame(render);
}

function activate(e){
	if(e.target === pathElement)
		active = true
}

function deactivate(e){
	active = false;
	if(status === IDLE)
		tip.set(originalTipPosition)
}

function watch(e,x,y){
	if(active && status === IDLE && !rendering)
		tip.set({ x, y })
}
</script>

{#if mounted}
	<svg
		use:init
		{width}
		{height}
		viewBox="0 0 {width} {height}"
		xmlns="http://www.w3.org/2000/svg"
		on:mousedown={activate}
		on:mouseup={deactivate}
		on:mousemove={(e)=>watch(e,e.clientX,e.clientY)}
		>
		<path bind:this={pathElement} d={path} fill="#000" stroke="black">
			hello
		</path>
	</svg>
{/if}