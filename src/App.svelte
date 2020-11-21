<script>
import FluidGaussian from "./FluidGaussian.svelte";
let width;
let height;
</script>

<svelte:window bind:innerWidth={width} bind:innerHeight={height} />

<FluidGaussian 
	{width}
	{height}
	onComplete={()=>{
		console.log("completed!");
	}}
	onButtonMove={(x,y)=>{
		console.log(x,y);
	}}
>
	<div slot="page" class="page">
		<img alt="img1" src="https://raw.githubusercontent.com/Cuberto/liquid-swipe/master/Example/liquid-swipe/Images.xcassets/secondPageImage.imageset/secondPageImage%403x.png">
		<div id="content">
			<div class="contentL1 text">For</div>
			<div class="contentL2 text">GAMERS</div>
			<div class="contentL3 text">Temporibus autem aut officiis debitis aut rerum necessitatibus.</div>
		</div>
	</div>
</FluidGaussian>

<style>
	:global(html,body){
		margin: 0;
		padding: 0;
	}

	img {
		width: 43vh;
		margin-left: 15px;
	}
	.page{
		background-image: linear-gradient(to right, #670185 0%, #430053 100%);
		font-family: "Oswald", sans-serif;
		text-align: center;
		font-size: 37px;
		font-weight: bold;
		flex-direction: column;
		display: flex;
		justify-content: space-between;
		padding: 0vh 13px;
		padding-top: 7vh;
	}
	.header {
		display: flex;
		justify-content: space-between;
		color: rgb(255, 255, 255);
		font-family: "Roboto", sans-serif;
		font-size: 19px;
		padding: 0px 31px;

		color: rgb(255, 255, 255);
	}

	#content{
		display: flex;
		flex-direction: column;
		padding: 0px 48px;
		height: 30vh;
	}
		
	.contentL1 {
		color: rgb(0, 0, 0);
		font-family: monospace;
		font-weight: 100;
		text-align: left;
		letter-spacing: -2px;
		line-height: 18px;
		opacity: 0.3;
	}

	.contentL2 {
		color: #000;
		font-family: "Oswald", sans-serif;
		margin-bottom: 8px;
		text-align: left;
	}

	.contentL3 {
		color: #000;
		font-family: "Roboto", sans-serif;
		text-align: left;
		font-size: 15px;
		font-weight: 100;
		word-spacing: 2px;
		letter-spacing: 1px;
		opacity: 0.5;
	}

	.text {
		color: #fd5888;
	}
</style>