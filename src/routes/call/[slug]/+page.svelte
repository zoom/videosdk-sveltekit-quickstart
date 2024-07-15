<script lang="ts">
	import type { PageData } from './$types';
	import { page } from '$app/stores';
	import Videocall from './Videocall.svelte';

	export let data: PageData;
	const JWT = data.JWT;
	export const slug = $page.url.pathname.split('/')[2];
</script>

<div class="flex flex-col">
	<script src="/coi-serviceworker.js"></script>
	<h1 class="text-center text-3xl font-bold my-4">Session: {slug}</h1>
	<Videocall {JWT} {slug} />
</div>
