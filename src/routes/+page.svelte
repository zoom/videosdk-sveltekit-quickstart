<script lang="ts">
	import { goto } from '$app/navigation';

	let slug = $state('');

	const handleClick = () => slug && goto(`/call/${slug}`);
</script>

<div class="flex flex-col">
	<h1 class="my-4 text-center text-3xl font-bold">Zoom VideoSDK SvelteKit Quickstart</h1>
	<div class="flex flex-col self-center">
		<input
			type="text"
			placeholder="Session Name"
			class="w-64 self-center rounded border border-gray-300 bg-gray-200 px-4 py-2 text-gray-700"
			bind:value={slug}
		/>
		<br />
		<button class="mb-4 w-64 self-center rounded bg-blue-500 px-4 py-2 font-bold text-white" onclick={handleClick}>
			Create
		</button>
	</div>
</div>
