<script lang="ts">
import { spring } from "svelte/motion";
export const LEFT:number = 0;
export const RIGHT:number = 1;
export const TOP:number = 2;
export const BOTTOM:number = 3;
export let width:number = 500;
export let height:number = 500;
export let orientation:number = LEFT;
export let position:Record<string,number> = { x: 50, y: 250 };
export let triggerValue:number = 250;
export let step:number = 15;


let sliding:boolean = false;
let mousedown:boolean = false;
let ctx:CanvasRenderingContext2D;
let cv:HTMLCanvasElement;
let size:number = 100;
let originalTipPosition:Record<string,number>;
let tip = spring( originalTipPosition, { stiffness: 0.1, damping: 0.2 } );

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
function renderLeft(x,y):void{
	let nx:number;
	for (let ny=0; ny < cv.height; ny += step){
		nx = x/Math.pow(Math.E, (Math.pow(ny-y, 2))/(2*size*size));
		ctx.lineTo(deltaLeft.base + nx,cv.height-ny);
		ctx.stroke();
	}

	if(x >= triggerValue){
		sliding = true;
		deltaLeft.slide = x;
	}
	
}

function renderRight(x,y):void{

	x = cv.width - x;

	let nx:number;
	for (let ny = 0; ny < cv.height; ny += step){
		nx = cv.width - (x/Math.pow(Math.E, (Math.pow(ny-y, 2))/(2*size*size)));

		ctx.lineTo(nx,cv.height-ny);
		ctx.stroke();
	}
}

function renderBottom(x,y):void{
	let ny:number;
	for (let nx = 0; nx < cv.width; nx += step){
		ny = cv.height - (y/Math.pow(Math.E, (Math.pow(nx-x, 2))/(2*size*size)));

		ctx.lineTo(nx,ny);
		ctx.stroke();
	} 
}

function renderTop(x,y):void{

	y = cv.height - y;

	let ny:number;
	for (let nx = 0; nx < cv.width; nx += step){
		ny = y/Math.pow(Math.E, (Math.pow(nx-x, 2))/(2*size*size));

		ctx.lineTo(nx,ny);
		ctx.stroke();
	} 
}

//let lastX:number=-1,lastY:number=-1;
function render():void{
	ctx.beginPath();
	ctx.clearRect(0,0,cv.width,cv.height);
	

	let x:number = $tip.x;
	let y:number = cv.height - $tip.y;

	//if((lastX !== x || lastY !== y) || (lastX === -1 && lastY === -1)){
		//lastX = x;
		//lastY = y;
		switch(orientation){
			case LEFT: renderLeft(x,y); break;
			case RIGHT: renderRight(x,y); break;
			case TOP: renderTop(x,y); break;
			case BOTTOM: renderBottom(x,y); break;
		}
	//}

	ctx.closePath();

	
	
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
		//if(!sliding)
			$tip = originalTipPosition
	}}
	on:mousemove={e=>{
		//if(mousedown && !sliding)
		if(mousedown)
			$tip = {
				x:e.clientX,
				y:e.clientY
			}
	}}
></canvas>