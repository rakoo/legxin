<style>
	.flex-container {
		display: flex;
		flex-wrap: wrap;
	}

	.item {
		width: 600px;

		margin-top: 10px;
		margin: 10px;
		text-align: left;
		font-family: monospace;
		font-size: 13px;
		border: 3px solid #6FC3DF;
		border-radius: 3px;

		display: grid;
		grid-template-columns: auto;
	}

	.item:hover() {
		border: 3px solid #E6FFFF;
	}

	.item:hover() .content {
		border: 3px solid #E6FFFF;
	}

	.item > * {
		margin: 3px;
	}

	.titlebar {
		font-weight: bold;
		margin-top: 10px;
		height: 12px;

		display: grid;
		grid-template-columns: auto auto;
	}
	.titlebar > * {
		margin-top: 0px;
	}

	.flairs {
		text-align: right;
	}

	.content {
		height: 160px;
		display: grid;
		grid-template-columns: 150px auto;
		align-items: center;
		border: 1px solid #6FC3DF;
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

	a {
		color: #E6FFFF;
	}

</style>

<script>
	import { onMount } from 'svelte';

	export let r;

	let posts = [];
	let waiting = true;

	onMount(async () => {
		const subreddit = new URL(window.location.href).searchParams.get("subreddit");
		posts = await r.getHot(subreddit || '');
		waiting = false;
	})

	function thumbnailOrLink(post) {
		return post.thumbnail == "self" || post.thumbnail == "default" || post.thumbnail == "image" ? "" : post.thumbnail
	}

	function flairs(post) {
		console.log(post)
		return post.link_flair_richtext.map(f => f.t).join(',').toUpperCase()
	}

	function linkForSubreddit(sr) {
		return new URL('?subreddit=' + sr.split('/')[1], window.location).toString()
	}

</script>

<div class="flex-container">
	{#if waiting}
		<p>Loading...</p>
	{:else}
		{#each posts as p, i}
			<div class="item">
				<div class=titlebar>
					<a href={linkForSubreddit(p.subreddit_name_prefixed)}>/{p.subreddit_name_prefixed}/</a>
					<p class="flairs">{flairs(p)}</p>
				</div>
				<div class=content>
					<a href={p.url}>
						<img src={thumbnailOrLink(p)} height={p.thumbnailHeight} width={p.thumbnailWidth} alt="">
					</a>
					<p>{p.title}</p>
					<p class="author">/u/{p.author.name}</p>
				</div>
			</div>
		{/each}
	{/if}
</div>
