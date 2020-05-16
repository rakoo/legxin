<style>
	.flex-container {
		display: flex;
		flex-wrap: wrap;
	}

	.item {
		position: relative;
		margin: 0px auto;

		display: grid;
		grid-template-columns: 200px auto;
	}

	.front {
		height: 300px;
		color: #FFF;
		margin: 10px;
		text-align: center;
		font-size: 11px;
		background-color: #111;
		border: 3px solid #87CBD4;

		grid-row-start: 2;
		grid-row-end: 3;
		grid-column-start: 1;
		grid-column-end: 2;		
	}

	.right {
		width: 10px;
		height: 300px;
		background-color: #111;
		border: 3px solid #87CBD4;

		grid-row-start: 2;
		grid-row-end: 3;
		grid-column-start: 2;
		grid-column-end: 3;

		transform: skew(0deg, -50deg) translate(-10px, -2px);
		transform-origin: bottom left;
	}
	
	.top {
		height: 12.6px;
		width: 174.2px;
		background-color: #111;
		border: 3px solid #87CBD4;

		grid-row-start: 1;
		grid-row-end: 2;
		grid-column-start: 1;
		grid-column-end: 2;

		transform: skew(-40deg, 0deg) translate(18px, 9.7px);
		transform-origin: bottom left;
	}

	.content {
		margin-top: 10px;
	}


</style>

<script>
	import { onMount } from 'svelte';

	export let r;

	let hot = [];

	onMount(async () => {
		hot = await r.getHot();
		console.log(hot)
	})

</script>

<div class="flex-container">
	{#each hot as p, i}
		<div class="item">
			<div class="front">
				<div class="content">
					<div>
						<a href={p.url}>
							<img src={p.thumbnail || ""} height={p.thumbnailHeight} width={p.thumbnailWidth} alt="">
						</a>
					</div>
					<p>{p.title}</p>
				</div>
			</div>
			<div class="right"></div>
			<div class="top"></div>
			</div>
	{/each}
</div>
