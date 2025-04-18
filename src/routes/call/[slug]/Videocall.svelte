<script lang="ts">
	import { onMount } from 'svelte';
	import ZoomVideo, { type VideoPlayer, VideoQuality } from '@zoom/videosdk';

	let { JWT, slug } = $props();

	const username = `User-${String(new Date().getTime()).slice(6)}`;
	const client = ZoomVideo.createClient();
	let disableStart = $state(false);
	let inSession = $state(false);
	let audioMuted = $state(false);
	let videoMuted = $state(false);
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
			if (Array.isArray(element)) {
				element.forEach((el) => el.remove());
			} else {
				element.remove();
			}
		} else {
			const userVideo = await mediaStream.attachVideo(event.userId, VideoQuality.Video_360P);
			videoContainer?.appendChild(userVideo as VideoPlayer);
		}
	};

	const leaveCall = async () => {
		const mediaStream = client.getMediaStream();
		for (const user of client.getAllUser()) {
			const element = await mediaStream.detachVideo(user.userId);
			if (Array.isArray(element)) {
				element.forEach((el) => el.remove());
			} else {
				element.remove();
			}
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
		class="margin-auto flex h-[75vh] w-[80vw] self-center overflow-hidden"
	>
		<video-player-container bind:this={videoContainer}></video-player-container>
	</div>
	{#if !inSession}
		<div class="mx-auto flex w-64 flex-col self-center">
			<button
				class={`${!disableStart ? 'bg-blue-500' : 'bg-gray-300'} mb-4 w-64 self-center rounded px-4 py-2 font-bold text-white`}
				onclick={startCall}
				disabled={disableStart}
			>
				Join
			</button>
		</div>
	{:else}
		<div class="flex w-full flex-col justify-around self-center">
			<div class="m-2 flex flex-row self-center">
				<button onclick={toggleVideo} class="mx-2 w-64 self-center rounded bg-blue-500 px-4 py-2 font-bold text-white">
					<p>
						{#if videoMuted}
							Unmute Video
						{:else}
							Mute Video
						{/if}
					</p>
				</button>
				<button onclick={toggleAudio} class="mx-2 w-64 self-center rounded bg-blue-500 px-4 py-2 font-bold text-white">
					<p>
						{#if audioMuted}
							Unmute Audio
						{:else}
							Mute Audio
						{/if}
					</p>
				</button>
				<button onclick={leaveCall} class="mx-2 w-64 self-center rounded bg-blue-500 px-4 py-2 font-bold text-white">
					<p>Leave</p>
				</button>
			</div>
		</div>
	{/if}
</div>
