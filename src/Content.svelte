<style>
	.flex-container {
		display: flex;
		flex-wrap: wrap;
		margin-left: 30px;
	}

	.item {
		width: 30%;
		min-width: 400px;

		margin: 10px;
		text-align: left;
		font-size: 18px;
		border: 1px solid #6FC3DF;
		border-bottom: 0px;
		border-left: 0px;
		border-radius: 5px;
		background-color: #070A0F;
		box-shadow: 0 0 6px #6FC3DF;

		display: grid;
		grid-template-columns: auto;

		height: 90%;
		display: grid;
		grid-template-columns: 150px auto;
		align-items: center;
	}

	.item:hover {
		border: 1px solid #E6FFFF;
		border-bottom: 0px;
		border-left: 0px;
		box-shadow: 0 0 6px #E6FFFF;
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

	.footer {
		display: grid;
		grid-template-columns: 50% 50%;
		align-items: center;
		margin-top: 30px;
	}

	.button {
		border: 3px solid #E6FFFF;
		text-align: center;
		width: 40%;
		display: grid;
		align-items: center;
		height: 30px;
		border-radius: 3px;
		text-decoration: none;
		box-shadow: 0 0 6px #E6FFFF;
	}

	.button:hover {
		background-color: #E6FFFF;
		color: #070A0F;
		border-radius: 3px;
		text-decoration: none;
	}

	.prev {
		grid-column-start: 1;
		grid-column-end: 2;
		margin-left: 40%;
	}

	.next {
		grid-column-start: 2;
		grid-column-end: 3;
		margin-left: 20%;
	}

</style>

<script>
	import { onMount } from 'svelte'
	import moment from 'moment'
	import { fade } from 'svelte/transition'

	export let r

	let posts = []
	let waiting = true
	let linkForPrev, linkForNext
	let showLinks

	onMount(load)

	async function load() {
		const after = new URL(window.location.href).searchParams.get("after") || ""
		const before = new URL(window.location.href).searchParams.get("before") || ""

		waiting = true
		posts = await r.getHot(getCurrentListingContext(), {after: after, before: before})
		posts = addThumbnails(posts)
		waiting = false

		linkForPrev = link('prev')
		linkForNext = link('next')
		showLinks = posts.length > 0
	}

	function addThumbnails(posts) {
		return posts.map(p => {
			p.has_thumbnail = p.thumbnail != "self" && p.thumbnail != "default" && p.thumbnail != "image" && p.thumbnail != "nsfw"
			return p
		})
	}

	function getCurrentListingContext() {
		return new URL(window.location.href).searchParams.get("subreddit") || ""
	}

	function flairs(post) {
		return post.link_flair_richtext.map(f => f.t).join(',').toUpperCase()
	}

	function linkForSubreddit(sr) {
		return new URL('?subreddit=' + sr.split('/')[1], window.location).toString()
	}

	function creationTime(post) {
		const m_server = moment.unix(post.created_utc)
		const m_client = moment()

		// If the time of the server is > our time, pick our time so we don't display "in 4 seconds"
		return moment.min(m_server, m_client).fromNow()
	}

	function link(prevOrNext) {
		if (posts.length == 0) return new URL('/', window.location)

		if (prevOrNext == 'prev') {
			return new URL('?before=' + posts[0].name, window.location)
		} else if (prevOrNext == 'next') {
			return new URL('?after=' + posts[posts.length - 1].name, window.location)
		}
	}

	function clickPrev() {
		history.pushState('', '', link('prev'))
		load()
		return false
	}

	function clickNext() {
		history.pushState('', '', link('next'))
		load()
		return false
	}

</script>

<svelte:window on:popstate={load}/>

{#if waiting}
	<p>Loading...</p>
{:else if posts.length == 0}
	<h1>Nothing!</h1>
{:else}
	<div class="flex-container">
		{#each posts as p, i}
			<div class="item" transition:fade="{{duration:100}}">
				<a href={p.url} class="thumbnail">
					{#if p.has_thumbnail}
						<img src={p.thumbnail} height={p.thumbnail_height} width={p.thumbnail_width} alt=""/>
					{:else}
						*
					{/if}
				</a>
				<p>{p.title}</p>
				<p class="author">{creationTime(p)} by /u/{p.author.name} to <a href={linkForSubreddit(p.subreddit_name_prefixed)}>/{p.subreddit_name_prefixed}/</a></p>
			</div>
		{/each}
	</div>

	<div class="footer">
		{#if showLinks}
			<a class="button prev" href={linkForPrev} on:click|preventDefault={clickPrev}><b>Prev<b></a>
			<a class="button next" href={linkForNext} on:click|preventDefault={clickNext}><b>Next<b></a>
		{/if}
	</div>
{/if}
