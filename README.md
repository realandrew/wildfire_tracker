# Wildfire Tracker

A website by Andrew Mitchell that maps wildfires from the NASA EONET API.

Note: The non-authenticated version of the NASA API has limits of 30 requests per IP per hour and 50 requests per IP per day. If you wish to run this map 24/7 (say on a second monitor), I suggest you obtain an API key from [NASA APIs](https://api.nasa.gov/) and add it as a parameter to the URL in the `fetch` method inside the `updateWildfires` function in `components/wildfiremap.vue` (so put `&api_key=<your_api_key>` at the end of the URL). In the future I may make this an env var so you don't have to modify the code.

## Utilizing the following

- Nuxt3 [link](https://v3.nuxtjs.org)
- modern-normalize [link](https://github.com/sindresorhus/modern-normalize)
- OpenLayers [link](https://openlayers.org/)

## Freely licensed under the MIT licensed

Feel free to use this project however you wish, it's freely available under the MIT license.
See the [LICENSE.txt](LICENSE.txt).

This (obviously) means any code contributed to this project will also be available under the same license and that by contributing to this project you have the rights to and agree to having the code licensed as such.

## Setup

Make sure to install the dependencies:

```bash
# yarn
yarn install

# npm
npm install

# pnpm
pnpm install --shamefully-hoist
```

## Development Server

Start the development server on http://localhost:3000

```bash
npm run dev
```

## Production

Build the application for production:

```bash
npm run build
```

Locally preview production build:

```bash
npm run preview
```

Checkout the [deployment documentation](https://v3.nuxtjs.org/docs/deployment) for more information.
