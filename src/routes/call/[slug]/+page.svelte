<script lang="ts">
	import { page } from '$app/state';
	import Videocall from './Videocall.svelte';

	let { data } = $props();
	const JWT = data.JWT;
	const slug = page.url.pathname.split('/')[2];
</script>

<div class="flex flex-col">
	<script src="/coi-serviceworker.js"></script>
	<h1 class="my-4 text-center text-3xl font-bold">Session: {slug}</h1>
	<Videocall {JWT} {slug} />
</div>
