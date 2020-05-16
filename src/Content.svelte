<style>
	.flex-container {
		display: flex;
		flex-wrap: wrap;
	}

	.item {
		height: 150px;
		width: 400px;

		margin-top: 10px;
		color: #FFF;
		margin: 10px;
		text-align: left;
		font-size: 11px;
		background-color: #111;
		border: 10px double #87CBD4;

		grid-row-start: 2;
		grid-row-end: 3;
		grid-column-start: 1;
		grid-column-end: 2;		

		display: grid;
		grid-template-columns: 150px auto;
		align-items: center;
	}

	.item > * {
		margin: 10px;
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
			<div>
				<a href={p.url}>
					<img src={p.thumbnail || ""} height={p.thumbnailHeight} width={p.thumbnailWidth} alt="">
				</a>
			</div>
			<p>{p.title}</p>
		</div>
	{/each}
</div>
