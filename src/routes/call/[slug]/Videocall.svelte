<script lang="ts">
	import { onMount } from 'svelte';
	import ZoomVideo, { type VideoPlayer, VideoQuality } from '@zoom/videosdk';

	export let JWT: string;
	export let slug: string;

	const username = `User-${String(new Date().getTime()).slice(6)}`;
	const client = ZoomVideo.createClient();
	let disableStart = false;
	let inSession = false;
	let audioMuted = false;
	let videoMuted = false;
	let videoContainer: HTMLElement;

	onMount(async () => {
		await client.init('en-US', 'Global', { patchJsMedia: true });
	});

	const startCall = async () => {
		disableStart = true;
		client.on('peer-video-state-change', renderVideo);
		await client.join(slug, JWT, username);
		const mediaStream = client.getMediaStream();
		await mediaStream.startAudio();
		await mediaStream.startVideo();
		await renderVideo({ action: 'Start', userId: client.getCurrentUserInfo().userId });
		inSession = true;
		disableStart = false;
		audioMuted = mediaStream.isAudioMuted();
		videoMuted = !mediaStream.isCapturingVideo();
	};

	const renderVideo = async (event: { action: 'Start' | 'Stop'; userId: number }) => {
		const mediaStream = client.getMediaStream();
		if (event.action === 'Stop') {
			const element = await mediaStream.detachVideo(event.userId);
			Array.isArray(element) ? element.forEach((el) => el.remove()) : element.remove();
		} else {
			const userVideo = await mediaStream.attachVideo(event.userId, VideoQuality.Video_360P);
			videoContainer?.appendChild(userVideo as VideoPlayer);
		}
	};

	const leaveCall = async () => {
		const mediaStream = client.getMediaStream();
		for (const user of client.getAllUser()) {
			const element = await mediaStream.detachVideo(user.userId);
			Array.isArray(element) ? element.forEach((el) => el.remove()) : element.remove();
		}
		client.off('peer-video-state-change', renderVideo);
		await client.leave();
		inSession = false;
	};

	const toggleVideo = async () => {
		const mediaStream = client.getMediaStream();
		if (mediaStream.isCapturingVideo()) {
			await mediaStream.stopVideo();
			await renderVideo({ action: 'Stop', userId: client.getCurrentUserInfo().userId });
		} else {
			await mediaStream.startVideo();
			await renderVideo({ action: 'Start', userId: client.getCurrentUserInfo().userId });
		}
		videoMuted = !mediaStream.isCapturingVideo();
	};

	const toggleAudio = async () => {
		const mediaStream = client.getMediaStream();
		if (client.getCurrentUserInfo().muted) {
			await mediaStream.unmuteAudio();
		} else {
			await mediaStream.muteAudio();
		}
		audioMuted = mediaStream.isAudioMuted();
	};
</script>

<div class="flex h-full w-full flex-1 flex-col">
	<div
		style={!inSession ? 'display: none;' : 'display: flex;'}
		class="flex max-h-[75vh] w-[80vw] overflow-hidden self-center margin-auto"
	>
		<video-player-container bind:this={videoContainer}></video-player-container>
	</div>
	{#if !inSession}
		<div class="mx-auto flex w-64 flex-col self-center">
			<button
				class={`${!disableStart ? 'bg-blue-500' : 'bg-gray-300'} text-white font-bold py-2 px-4 rounded mb-4 w-64 self-center`}
				on:click={startCall}
				disabled={disableStart}
			>
				Join
			</button>
		</div>
	{:else}
		<div class="flex w-full flex-col justify-around self-center">
			<div class="flex flex-row self-center m-2">
				<button on:click={toggleVideo} class="bg-blue-500 text-white font-bold py-2 px-4 rounded mx-2 w-64 self-center">
					<p>
						{#if videoMuted}
							Unmute Video
						{:else}
							Mute Video
						{/if}
					</p>
				</button>
				<button on:click={toggleAudio} class="bg-blue-500 text-white font-bold py-2 px-4 rounded mx-2 w-64 self-center">
					<p>
						{#if audioMuted}
							Unmute Audio
						{:else}
							Mute Audio
						{/if}
					</p>
				</button>
				<button on:click={leaveCall} class="bg-blue-500 text-white font-bold py-2 px-4 rounded mx-2 w-64 self-center">
					<p>Leave</p>
				</button>
			</div>
		</div>
	{/if}
</div>
