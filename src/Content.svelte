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
		border: 1px solid #6FC3DF;
		border-radius: 5px;
		background-color: #070A0F;

		display: grid;
		grid-template-columns: auto;

		height: 90%;
		display: grid;
		grid-template-columns: 80px auto;
		align-items: center;
	}

	.item:hover {
		border: 1px solid #E6FFFF;
	}

	.item > * {
		margin: 6px;
	}

	.flairs {
		text-align: right;
	}

	.item:hover .content {
		border: 1px solid #E6FFFF;
	}

	.item > a {
		grid-column-start: 1;
		grid-column-end: 2;
		grid-row-start: 1;
		grid-row-end: 3;

		display: grid;
		align-items: center;
	}

	.author {
		text-align: right;
		font-size: 12px;
	}

	a {
		color: #E6FFFF;
	}
	.thumbnail {
		font-size:100px;
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
		posts = posts.map(p => {
			p.has_thumbnail = p.thumbnail != "self" && p.thumbnail != "default" && p.thumbnail != "image"
			return p;
		})
		waiting = false;
	})

	function flairs(post) {
		console.log(post)
		return post.link_flair_richtext.map(f => f.t).join(',').toUpperCase()
	}

	function linkForSubreddit(sr) {
		return new URL('?subreddit=' + sr.split('/')[1], window.location).toString()
	}

	function creationTime(post) {
		const m_server = moment.unix(post.created_utc)
		const m_client = moment();

		// If the time of the server is > our time, pick our time so we don't display "in 4 seconds"
		return moment.min(m_server, m_client).fromNow();
	}

</script>

<div class="flex-container">
	{#if waiting}
		<p>Loading...</p>
	{:else}
		{#each posts as p, i}
			<div class="item">
				<a href={p.url} class="thumbnail">
					{#if p.has_thumbnail}
						<img src={p.thumbnail} height={p.thumbnail_height/2} width={p.thumbnail_width/2} alt=""/>
					{:else}
						*
					{/if}
				</a>
				<p>{p.title}</p>
				<p class="author">{creationTime(p)} by /u/{p.author.name} to <a href={linkForSubreddit(p.subreddit_name_prefixed)}>/{p.subreddit_name_prefixed}/</a></p>
			</div>
		{/each}
	{/if}
</div>
