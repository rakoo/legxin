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
	import { gapi } from './gapi';
	import { onMount } from 'svelte';
	import Content from './Content.svelte';

	let apiKeyInput, apiKey;
	let clientIdInput, clientId;

	let signedIn = false;

	onMount(async () => {
		handleClientLoad()
	})

	function setApiKey() {
		if (!apiKeyInput.files) return;
		let file = apiKeyInput.files[0];
		if (!file) return;
		let reader = new FileReader();
		reader.onload = (evt) => {
			apiKey = evt.target.result.trim();
		}
		reader.readAsText(file, 'UTF-8');
	}

	function setClientId() {
		if (!clientIdInput.files) return;
		let file = clientIdInput.files[0];
		if (!file) return;
		let reader = new FileReader();
		reader.onload = (evt) => {
			clientId = JSON.parse(evt.target.result).web.client_id
		}
		reader.readAsText(file, 'UTF-8');
	}


	/**
	 *  On load, called to load the auth2 library and API client library.
	 */
	function handleClientLoad() {
		gapi.load('client:auth2', handleAuth);
	}

	/**
	 *  Initializes the API client library and sets up sign-in state
	 *  listeners.
	 */
	async function handleAuth() {
		// Array of API discovery doc URLs for APIs used by the quickstart
		var DISCOVERY_DOCS = ["https://www.googleapis.com/discovery/v1/apis/gmail/v1/rest"];

		// Authorization scopes required by the API; multiple scopes can be
		// included, separated by spaces.
		var SCOPES = 'https://www.googleapis.com/auth/gmail.readonly';

		if (!apiKey || !clientId) {
			return;
		}

		gapi.client.init({
			apiKey: apiKey,
			clientId: clientId,
			discoveryDocs: DISCOVERY_DOCS,
			scope: SCOPES
		}).then(() => {
			// Listen for sign-in state changes.
			gapi.auth2.getAuthInstance().isSignedIn.listen(updateSigninStatus);

			// Handle the initial sign-in state.
			gapi.auth2.getAuthInstance().signIn();
			updateSigninStatus(gapi.auth2.getAuthInstance().isSignedIn.get());
		}, (error) => {
			console.log(JSON.stringify(error, null, 2));
		});
	}

	/**
	 *  Called when the signed in status changes, to update the UI
	 *  appropriately. After a sign-in, the API is called.
	 */
	function updateSigninStatus(isSignedIn) {
		if (isSignedIn) {
			signedIn = true;
		} else {
			signedIn = false;
		}
	}

	/**
	 *  Sign out the user upon button click.
	 */
	function handleSignoutClick(event) {
		gapi.auth2.getAuthInstance().signOut();
	}

</script>

<main>
	<div id="gapi" style="display: none;"></div>
	<h1>LMail</h1>

	{#if signedIn}
		<button on:click={handleSignoutClick}>Sign Out</button>
		<Content gapi={gapi} signedIn={signedIn}/>
	{:else}

		<table border="1">
			<tbody>
				<tr>
					<th>API_KEY</th>
					<td><input type="file" bind:this={apiKeyInput} on:change={setApiKey}></td>
				</tr>
				<tr>
					<th>credentials.json</th>
					<td><input type="file" bind:this={clientIdInput} on:change={setClientId}></td>
				</tr>
				<tr>
					<th>Log in</th>
					<td><button on:click={handleAuth}>Log In</button></td>
				</tr>
			</tbody>
		</table>
	{/if}
</main>
