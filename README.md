This is a relay server for usage with [OpenAI's realtime API](https://platform.openai.com/docs/guides/realtime/quickstart). You can use this repo as a starting point for incorporating the realtime api into your own project. When deployed to a Cloudflare Worker, you can connect to it from a frontend (like a web app) without needing your users to have or share their own OpenAI api key.

_NB: This project is considered beta, because the underlying OpenAI Realtime API is currently in beta. We will work on keeping this in sync with the work at OpenAI, but there may be some lag. File an issue if you have any problems!_

## Usage

The included [server](./src/server.ts) should work out of the box with the official example [openai-realtime-console](https://github.com/openai/openai-realtime-console/) application. You can copy paste code out of it into your Worker, or just deploy this one directly. Feel free to modify the code and configuration to fit your needs.

### Add an OPENAI_API_KEY to your secrets

To actually connect to OpenAI, you'll need to add your OpenAI API key to the secrets for your worker. First, generate an API key from your OpenAI console at https://platform.openai.com/api-keys.

In local development, create a file called `.dev.vars` in the root of the project with the following:

```
OPENAI_API_KEY=<your key here>
```

This should let you develop and test your code locally. The API key will be available in your request's `env` object as `env.OPENAI_API_KEY`.

In production, you'll need to add an `OPENAI_API_KEY` to your Worker's secrets. After that, you can add it to your secrets by running the following command:

```sh
npx wrangler secret put OPENAI_API_KEY
```

Then, after you deploy your project, the API key will be available in your request's `env` object as `env.OPENAI_API_KEY`.

### Add authorisation, rate limiting, or anything else!

We recommend that you add a layer of authorisation on top of this relay server. This will prevent any random person from using relay, and your OpenAI key! If you're deploying this relay server as part of another Workers project, then it should be fairly straightforward to leverage the authorisation and rate limiting already in place there.

If you're deploying this server to a completely different domain, then you'll have to add a layer of auth on top of the WebSocket connection. Since the standard WebSocket api doesn't support adding cookies or any other headers, you'll have to layer it on top of the URL/protocols when making the connection. For example, you could generate an encrypted short lived token in your existing webapp, add it to the WebSocket url's query params, and then decode it in the Worker to verify the provenance of the request.

## Show us what you made!

We'd love to see what you create with this. Tweet us [@cloudflaredev](https://twitter.com/cloudflaredev) and let us know what you're building!

## Thanks

<svg fill="none" width="170" height="25.616438356164384" viewBox="0 0 219 33"><path fill="currentColor" fill-rule="evenodd" d="M5.97401.919922H.382812V26.422H5.39992c.44113 0 .79874.3576.79874.7987v4.7332h6.76354c6.8044 0 12.0749-5.5161 12.0749-12.3205 0-6.8044-5.2705-12.32041-12.0749-12.32041-3.07483 0-5.43789 1.52303-6.98819 3.95571V.919922ZM6.23298 19.6334c0-3.7164 3.01279-6.7292 6.72922-6.7292 3.7165 0 6.7293 3.0128 6.7293 6.7292 0 3.7165-3.0128 6.7293-6.7293 6.7293H7.03172c-.44113 0-.79874-.3576-.79874-.7987v-5.9306Z" clip-rule="evenodd"></path><path fill="currentColor" d="M160.878 32.9281c-6.113 0-9.863-3.9044-9.863-10.6857V7.08708h6.113V21.6259c0 3.9044 1.747 5.7538 4.932 5.7538 3.288 0 5.394-2.3118 5.394-5.9593V7.08708h6.114V32.1575h-5.394v-2.4146c-1.644 2.1064-4.367 3.1852-7.296 3.1852Zm-74.9994-.9582V6.89952h5.3942v2.92831c1.4899-2.46595 4.3154-3.69892 7.5006-3.69892 6.1646 0 9.9666 3.90439 9.9666 10.68579v15.1552h-6.114V17.4312c0-3.9045-1.849-5.7539-5.0856-5.7539-3.3393 0-5.5483 2.3632-5.5483 5.9593v14.3333h-6.1135ZM42.9801 6.89958h-3.1859c-3.1338 0-5.086 1.59258-5.7025 4.36682V6.89958h-5.3943V31.97h6.1135V16.9688c0-3.1338 1.3357-4.675 4.3154-4.675h3.8538V6.89958Zm105.0069 0h-3.186c-3.134 0-5.086 1.59258-5.703 4.36682V6.89958h-5.394V31.97h6.114V16.9688c0-3.1338 1.335-4.675 4.315-4.675h3.854V6.89958ZM122.125.919922h-6.15V6.89958h-3.368v5.39422h3.025v13.3058c0 4.5723 2.004 6.3704 6.422 6.3704h4.95c.883 0 1.598-.7152 1.598-1.5975v-3.7968h-4.493c-1.747 0-2.363-.7192-2.363-2.3631V12.2938h6.856V6.89958h-6.477V.919922Zm89.285.187498h-6.15v5.97966h-2.825v5.39422h3.025v13.3058c0 4.5723 2.004 6.3704 6.422 6.3704h4.95c.882 0 1.597-.7152 1.597-1.5975v-3.7968h-4.492c-1.747 0-2.363-.7192-2.363-2.3631V12.4813h6.855V7.08708h-7.019V1.10742ZM177.533 26.2462c.863 4.3903 4.824 6.6819 11.034 6.6819 6.678 0 10.84-3.2366 10.84-8.2712 0-4.1613-2.723-6.9355-7.45-7.5006l-4.058-.4624c-2.517-.3082-3.699-1.1302-3.699-2.62 0-1.7467 1.644-2.7742 4.264-2.7742 2.757 0 4.499.9131 4.622 2.8907l5.947-1.293c-.867-4.36139-4.706-6.58099-10.466-6.58099-6.371 0-10.378 3.08243-10.378 8.06569 0 4.264 2.929 7.0382 7.604 7.552l4.007.4109c2.209.2055 3.442 1.0275 3.442 2.6201 0 1.8495-1.696 3.0311-4.47 3.0311-3.06 0-4.854-1.0128-5.081-3.0888l-6.158 1.3388ZM80.2799.919922h-6.15V7.06856h6.15V.919922ZM74.1299 31.97V10.0275h6.1134V31.97h-6.1134Z"></path><path fill="currentColor" fill-rule="evenodd" d="M55.6746 31.9539c2.7778 0 5.1447-1.5069 6.7715-3.917l.0004.4585c0 1.6953 1.8776 3.4702 3.6243 3.4702l3.2719.004c.4416.0005.7998-.3573.7998-.7988v-4.1321h-1.642c-.5138 0-.7738-.2569-.7738-.7706V12.8496H63.247c-.4412 0-.7988-.3576-.7988-.7988V7.31299h-6.7736c-6.8044 0-12.0748 5.51601-12.0748 12.32041 0 6.8044 5.2704 12.3205 12.0748 12.3205Zm6.7293-12.3205c0 3.7165-3.0128 6.7293-6.7293 6.7293-3.7165 0-6.7292-3.0128-6.7292-6.7293 0-3.7164 3.0127-6.7292 6.7292-6.7292h5.9305c.4412 0 .7988.3576.7988.7987v5.9305Z" clip-rule="evenodd"></path></svg>

We built this in collaboration with the folks at [Braintrust](https://www.braintrust.dev/).