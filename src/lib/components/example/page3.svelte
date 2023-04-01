<div class="page1">
	<img alt="img1" src="image.png" />
	<div class="content1">
		<div class="contentL1 text">(3) Hello world</div>
		<div class="contentL2 text">MAIN PAGE</div>
		<div class="contentL3 text">
			Temporibus autem aut officiis debitis aut rerum necessitatibus.
		</div>
	</div>
</div>

<style>
	img {
		width: 43vh;
		margin-left: 15px;
	}
	.page1 {
		background-image: linear-gradient(to right, #e65000 0%, #ff9600 100%);
		font-family: 'Oswald', sans-serif;
		text-align: center;
		font-size: 37px;
		font-weight: bold;
		flex-direction: column;
		display: flex;
		justify-content: space-between;
		padding: 0vh 13px;
		padding-top: 7vh;
		height: 100%;
		width: 100%;
	}
	.content1 {
		display: flex;
		flex-direction: column;
		padding: 0px 48px;
		height: 30vh;
	}
	.content1 .contentL1 {
		color: #fff;
		font-family: monospace;
		font-weight: 100;
		text-align: left;
		letter-spacing: -2px;
		line-height: 18px;
		opacity: 0.3;
	}
	.content1 .contentL2 {
		color: #fff;
		font-family: 'Oswald', sans-serif;
		margin-bottom: 8px;
		text-align: left;
	}
	.content1 .contentL3 {
		color: #fff;
		font-family: 'Roboto', sans-serif;
		text-align: left;
		font-size: 15px;
		font-weight: 100;
		word-spacing: 2px;
		letter-spacing: 1px;
		opacity: 0.5;
	}
</style>
