<script>
import { onMount, tick } from 'svelte';

import { cubicOut } from 'svelte/easing';
import { spring,tweened } from "svelte/motion";

export let btnText = ">";
export let btnColor = "#fff";
export let btnBorderColor = "rgba(255,255,255,0.5)";
export let width = 500;
export let height = 500;
export let step = 10;
export let position;
export let triggerValue = 250;
export let precision = 3;
export let slideDuration = 1000;
export let slideEasing = cubicOut;
export let buttonSize = 10;
export let veilExpansionStiffness = .03;
export let veilExpansionDamping = .1;
export let buttonExpansionStiffness = .05;
export let buttonExpansionDamping = .2;
export let onComplete;
export let onButtonMove;

function uuid(short=false){
	let dt = new Date().getTime();
	const BLUEPRINT = short?'xyxxyxyx':'xxxxxxxx-xxxx-yxxx-yxxx-xxxxxxxxxxxx';
	const RESULT = BLUEPRINT.replace(/[xy]/g, function(c) {
		 let r = (dt + Math.random()*16)%16 | 0;
		 dt = Math.floor(dt/16);
		 return (c=='x' ? r :(r&0x3|0x8)).toString(16);
	});
	return RESULT;
}

let id = `clip-${uuid()}`;

const IDLE = 0, START_SLIDING = 1, SLIDING = 2, DONE = 3;
let status = IDLE;
let active = false;

let btn;
let slide;
let tip;
let size;
let originalTipPosition;

let path = "";
let mounted = true;

let btnOpacity = 1.0;

async function onBoundsChange(width,height){
	mounted = false;
	await tick();
	mounted = true;
}

$:onBoundsChange(width,height);

function init(){
	if(!position)
		position = { x: 70, y: height-200 };

	if(!position.x)
		position.x = 70;

	if(!position.y)
		position.y = height-200;

	originalTipPosition = tweened({x:0,y:position.y}, {
		duration: slideDuration,
		easing: slideEasing
	});

	originalTipPosition.set(position);


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
		$originalTipPosition,
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
let lastX,lastY;
let rendering = false;
function render(){
	if(DONE === status) return;
	let x = $tip.x < $originalTipPosition.x?$originalTipPosition.x:$tip.x;
	let y = height + x - $tip.y;
	rendering = true;
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
	
	if(deltaLeft.base >= width){
		status = DONE;
		if(onComplete) onComplete();
	}else if(x !== lastX || y !== lastY){
		if(onButtonMove) onButtonMove(x,y);
	}

	s = $size.buttonSize + x * 0.5;

	btnOpacity = 1 + ($originalTipPosition.x - x)/100;

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
	lastX = x;
	lastY = y;
	requestAnimationFrame(render);
}

function activate(e){
	console.log(e);
	if(e.target === btn)
		active = true
}

function deactivate(e){
	active = false;
	if(status === IDLE)
		tip.set($originalTipPosition)
}

function watch(e,x,y){
	if(active && status === IDLE && !rendering && (lastX !== x || lastY !== y)){
		tip.set({ x, y });
		lastX = x;
		lastY = y;
	}
}
onMount(init);
</script>

{#if mounted}
	<div 
		class="wrapper"
		on:mousedown={activate}
		on:mouseup={deactivate}
		on:mousemove={(e)=>watch(e,e.clientX,e.clientY)}

		on:touchstart={activate}
		on:touchend={deactivate}
		on:touchcancel={deactivate}
		on:touchmove={e=>watch(e,e.touches[0].clientX,e.touches[0].clientY)}
	>
		<svg
			{width}
			{height}
			viewBox="0 0 {width} {height}"
		>
			<clipPath {id}>
				<path d={path} fill="rgba(0,0,0,0.5)" stroke="black"/>
			</clipPath>
		</svg>

		<div class="page" style="clip-path: url(#{id});">
			<slot name="page" />
		</div>

		<div bind:this={btn} class="btn noselection" style="opacity:{btnOpacity};color:{btnColor};border: 1px solid {btnBorderColor};left:{deltaLeft.base + ($tip.x < $originalTipPosition.x?$originalTipPosition.x:$tip.x)-55}px;top:{$tip.y-25-2.5}px">{btnText}</div>
	</div>
{/if}

<style>
	.noselection{
		-webkit-touch-callout: none; /* iOS Safari */
		-webkit-user-select: none; /* Safari */
		-khtml-user-select: none; /* Konqueror HTML */
		-moz-user-select: none; /* Old versions of Firefox */
			-ms-user-select: none; /* Internet Explorer/Edge */
				user-select: none; /* Non-prefixed version, currently
									supported by Chrome, Edge, Opera and Firefox */
	}

	.wrapper {
		position: fixed;
		left: 0;
		right: 0;
		top: 0;
		bottom: 0;
	}

	.btn{
		display: grid;
		justify-items: center;
		align-items: center;
		position: absolute;

		width: 50px;
		height: 50px;

		border-radius: 50%;
		font-family: "Oswald", sans-serif;
		background: transparent;

		position: relative;
	}
	
	.page {
		left:0;
		right:0;
		top:0;
		bottom:0;
		position: absolute;
		display: grid;
	}
	.page > *{
		width: 100%;
		height: 100%;
	}
	svg{
		position: absolute;
		left:0;
		top:0;
		width: 100%;
		height: 100%;
		z-index: 1;
		pointer-events: none;
	}
</style>
