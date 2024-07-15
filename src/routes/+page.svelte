<script lang="ts">
	import { goto } from '$app/navigation';

	let slug = '';

	const handleClick = () => slug && goto(`/call/${slug}`);
</script>

<div class="flex flex-col">
	<h1 class="text-3xl font-bold text-center my-4">Zoom VideoSDK Vue/Nuxt Quickstart</h1>
	<div class="flex self-center flex-col">
		<input
			type="text"
			placeholder="Session Name"
			class="bg-gray-200 text-gray-700 border border-gray-300 rounded py-2 px-4 w-64 self-center"
			bind:value={slug}
		/>
		<br />
		<button class="bg-blue-500 text-white font-bold py-2 px-4 rounded mb-4 w-64 self-center" on:click={handleClick}>
			Create
		</button>
	</div>
</div>
