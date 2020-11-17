<script lang="ts">
import { cubicOut, circOut } from 'svelte/easing';
import { spring,tweened } from "svelte/motion";

export let width:number = 500;
export let height:number = 500;
export let position:Record<string,number> = { x: 50, y: height + 150 };
export let expansion:number = 100;
export let triggerValue:number = 350;
export let step:number = 10;
export let duration:number = 1000;
export let stiffness:number = 0.05;
export let damping:number = 0.05;

let sliding:boolean = false;
let mousedown:boolean = false;
let ctx:CanvasRenderingContext2D;
let cv:HTMLCanvasElement;
let originalTipPosition:Record<string,number>;

let slide = tweened(0, {
	duration: duration,
	easing: circOut
});

let tip = spring( originalTipPosition, { stiffness, damping } );
let size = spring( {expansion}, { stiffness, damping } );

let path = "";

function init(canvas:HTMLCanvasElement):void{
	cv = canvas;
	ctx = cv.getContext("2d");
	originalTipPosition = position;
	
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


let lastX:number=-1;
let moving = false;
function render():void{
	ctx.beginPath();
	ctx.clearRect(0,0,cv.width,cv.height);
	
	let x:number = $tip.x;
	let y:number = cv.height - $tip.y;
	let s:number;
	if(!sliding && x > triggerValue){
		sliding = true;
		$slide = cv.width;
	}

	if(sliding){
		$size = {
			expansion: $size.expansion + x
		};
		deltaLeft.slide = x;
		deltaLeft.base = $slide;
	}
	
		
	s = $size.expansion;


	// start drawing
	let nx:number;
	for (let ny=0; ny < cv.height; ny += step){
		nx = x/Math.pow(Math.E, (Math.pow(ny-y, 2))/(2*s*s));
		ctx.lineTo(deltaLeft.base + nx,cv.height-ny);
		ctx.stroke();
	}
	ctx.closePath();
	//finish drawing
	
	requestAnimationFrame(render);
}
</script>

<canvas 
	use:init 
	{height}
	{width}
	on:mousedown={()=>mousedown=true}
	on:mouseup={()=>{
		mousedown=false;
		if(!sliding)
			$tip = originalTipPosition
	}}
	on:mousemove={e=>{
		//if(mousedown && !sliding)
		if(mousedown && !sliding)
			$tip = {
				x:e.clientX,
				y:e.clientY
			}
	}}
></canvas>