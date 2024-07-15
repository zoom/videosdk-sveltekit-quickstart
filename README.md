# Zoom VideoSDK SvelteKit Quickstart

This is a quickstart for using the Zoom Video SDK for [Svelte](https://svelte.dev/) using [SvelteKit](https://kit.svelte.dev/)

## Prerequisites:

- Node LTS
- NPM
- Zoom Video SDK [credentials](https://developers.zoom.us/docs/video-sdk/get-credentials/)

1. Clone the repository

```bash
git clone https://github.com/zoom/videosdk-sveltekit-quickstart.git
```

2. Create an `.env` file in the root directory

```bash
cd videosdk-sveltekit-quickstart
cp .env.example .env
```

Fill in the `ZOOM_SDK_KEY` and `ZOOM_SDK_SECRET` with your credentials.

3. Install the dependencies

```bash
npm install
```

4. Run the development server

```bash
bun run dev
```

```bash
# or start the server and open the app in a new browser tab
npm run dev -- --open
```

Open a browser and visit `http://localhost:5173/`.

## Building

To create a production version of your app:

```bash
npm run build
```

You can preview the production build with `npm run preview`.

> To deploy your app, you may need to install an [adapter](https://kit.svelte.dev/docs/adapters) for your target environment.
