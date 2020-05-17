<style>
	.flex-container {
		display: flex;
		flex-wrap: wrap;
		margin-left: 50px;
	}

	.item {
		width: 90%;
		min-width: 400px;

		margin: 10px;
		text-align: left;
		font-size: 18px;
		border: 3px solid #6FC3DF;
		border-radius: 5px;
		background-color: #070A0F;

		display: grid;
		grid-template-columns: auto;
	}

	.item:hover {
		border: 3px solid #E6FFFF;
	}

	.item > * {
		margin: 3px;
	}

	.titlebar {
		font-weight: bold;
		margin-bottom: 10px;
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
		height: 90%;
		display: grid;
		grid-template-columns: 150px auto;
		align-items: center;
		border: 1px solid #6FC3DF;
		border-radius: 5px;
	}

	.item:hover .content {
		border: 1px solid #E6FFFF;
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
		font-size: 12px;
	}

	a {
		color: #E6FFFF;
	}

</style>

<script>
	import { onMount } from 'svelte';
	import moment from 'moment';

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

	function creationTime(post) {
		return moment.unix(post.created_utc)
			.min() // If the time of the server is > our time, pick our time so we don't display "in 4 seconds"
			.fromNow();
	}

</script>

<div class="flex-container">
	{#if waiting}
		<p>Loading...</p>
	{:else}
		{#each posts as p, i}
			<div class="item">
				<div class=titlebar>
					<div>
						<a href={linkForSubreddit(p.subreddit_name_prefixed)}>/{p.subreddit_name_prefixed}/</a>
					</div>
					<p class="flairs">{flairs(p)}</p>
				</div>
				<div class=content>
					<a href={p.url}>
						<img src={thumbnailOrLink(p)} height={p.thumbnail_height/2} width={p.thumbnail_width/2} alt="">
					</a>
					<p>{p.title}</p>
					<p class="author">{creationTime(p)} by /u/{p.author.name}</p>
				</div>
			</div>
		{/each}
	{/if}
</div>
