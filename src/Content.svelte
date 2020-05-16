<style>
	.flex-container {
		display: flex;
		flex-wrap: wrap;
	}

	.item {
		height: 260px;
		width: 400px;

		margin-top: 10px;
		color: #FFF;
		margin: 10px;
		text-align: left;
		font-family: monospace;
		font-size: 13px;
		background-color: #111;
		border: 3px solid #87CBD4;
		border-radius: 3px;

		display: grid;
		grid-template-columns: auto;
	}

	.item > * {
		margin: 3px;
	}

	.titlebar {
		font-weight: bold;
		margin-top: 10px;
	}

	.content {
		height: 220px;
		display: grid;
		grid-template-columns: 150px auto;
		align-items: center;
		border: 1px solid #87CBD4;
	}

	.content > a {
		grid-column-start: 1;
		grid-column-end: 2;
		grid-row-start: 1;
		grid-row-end: 3;
	}

	.content > * {
		margin: 10px;
	}

	.author {
		text-align: right;
	}

</style>

<script>
	import { onMount } from 'svelte';

	export let r;

	let hot = [];

	onMount(async () => {
		hot = await r.getHot();
	})

	function titleWithAuthor(post) {
		return post.title + "\n\n" + post.author.name
	}

	function thumbnailOrLink(post) {
		return post.thumbnail == "self" || post.thumbnail == "default" || post.thumbnail == "image" ? "" : post.thumbnail
	}
</script>

<div class="flex-container">
	{#each hot as p, i}
		<div class="item">
			<div class=titlebar>/{p.subreddit_name_prefixed}/</div>
			<div class=content>
				<a href={p.url}>
					<img src={thumbnailOrLink(p)} height={p.thumbnailHeight} width={p.thumbnailWidth} alt="">
				</a>
				<p>{p.title}</p>
				<p class="author">/u/{p.author.name}</p>
			</div>
		</div>
	{/each}
</div>
