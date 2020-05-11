<style>
	.title {
		font-size: 10pt;
		line-height: 14pt;
	}

	.dot {
		min-width: 12px;
	}

	.subtext {
		font-size: 9pt;
	}

	.spacer {
		height: 5px;
	}
</style>

<script>
	import { onMount } from 'svelte';

	export let gapi;
	export let signedIn;

	$: listLabels({signedIn})

	let allThreads = [];
	let noThreadsForSure = false;

	/**
	 * Print all Labels in the authorized user's inbox. If no labels
	 * are found an appropriate message is printed.
	 */
	function listLabels(signedIn) {
		gapi.client.gmail.users.threads.list({
			'userId': 'me',
			'labelIds': ['INBOX', 'UNREAD']
		}).then((response) => {
			var threads = response.result.threads;
			if (threads) {
				for (var i = 0; i < threads.length; i++) {
					addThread(threads[i].id);
				}
			} else {
				noThreadsForSure = true
			}
		});
	}

	function addThread(threadId) {
		gapi.client.gmail.users.threads.get({
			'userId': 'me',
			'id': threadId,
			'format': 'metadata',
			'metadataHeaders': ['SUBJECT', 'FROM', 'DATE']
		}).then((response) => {
			let thread = response.result;
			if (thread) {
				let lastMessage = thread.messages[thread.messages.length - 1];
				let threadStruct = {
					from: getValueOrDefault(lastMessage.payload.headers.filter((h) => h.name == "From")),
					date: getValueOrDefault(lastMessage.payload.headers.filter((h) => h.name == "Date")),
					subject: getValueOrDefault(lastMessage.payload.headers.filter((h) => h.name == "Subject")),
				}
				allThreads = [...allThreads, threadStruct];
			}
		})
	}

	function getValueOrDefault(u) {
		if (u && u.length > 0) {
			return u[0].value;
		} else {
			return ""
		}
	}

</script>

{#if allThreads.length > 0}
	<table cellspacing="0" cellpadding="0" border="0">
		{#each allThreads as thread, i}
			<tr class="title">
				<td class="dot">•</td>
				<td></td>
				<td>{thread.subject}</td>
			</tr>
			<tr>
				<td colspan="2"></td>
				<td class="subtext">by {thread.from} on {thread.date}</td>
			</tr>
			<tr class="spacer"></tr>
		{/each}
	</table>
{:else if noThreadsForSure}
	<p>No unread threads in inbox</p>
{:else}
	<p>Fetching...</p>
{/if}
