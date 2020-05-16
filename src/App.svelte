<style>
	main {
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	table {
		margin: 10px 0 15px 0;
		border-collapse: collapse;
	}

	td {
		border: 1px solid #ddd;
		padding: 3px 10px;
	}

	th {
		border: 1px solid #ddd;
		padding: 5px 10px;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>

<script>
	import { onMount } from 'svelte';
	import snoowrap from './snoowrap-v1.min';
	import Content from './Content.svelte';

	let signedIn = false;
	let waiting = false;
	let r;
	let hot = [];

	onMount(async () => {
		// Case 1: we are in part 2 of OAuth flow
		const code = new URL(window.location.href).searchParams.get('code')
		// TODO: check state
		if (code) { 
			window.history.replaceState({}, document.title, "/");
			const rr = await window.snoowrap.fromAuthCode({
				code: code,
				userAgent: 'snoowrap:github.com/rakoo/legin:v0.1 by /u/rakoo',
				clientId: 'i4ji8frakYoSkw',
				redirectUri: 'https://localhost:8000',
			})

			if (rr) {
				window.localStorage.setItem('refreshToken', rr.refreshToken);
				r = rr
			}
		} else {

			// Case 2: we already have a refresh token
			const refreshToken = window.localStorage.getItem('refreshToken')
			if (refreshToken) {
				const rr = new window.snoowrap({
					userAgent: 'snoowrap:github.com/rakoo/legin:v0.1 by /u/rakoo',
					clientId: 'i4ji8frakYoSkw',
					clientSecret: '',
					refreshToken: refreshToken
				})

				// Ping to make sure connection is on
				await rr.getMe()
				if (rr) {
					r = rr
				}
			}
		}

		if (r) {
			signedIn = true;
			waiting = true
			hot = await r.getHot();
			waiting = false;
		}
	})

	// Start the OAuth flow
	async function login() {
		const authUrl = window.snoowrap.getAuthUrl({
			clientId: 'i4ji8frakYoSkw',
			scope: ['read', 'submit', 'vote'],
			redirectUri: 'https://localhost:8000',
			state: 'foobar'
		});
		window.location = authUrl
	}

</script>

<main>
	<h1>Leĝin</h1>
	{#if !signedIn}
		<button on:click={login}>Login</button>
	{:else if waiting}
		<h2>Loading...</h2>
	{:else}
		<ul>
			{#each hot as p, i}
				<li>{p.title}</li>
			{/each}

		</ul>
	{/if}
</main>
