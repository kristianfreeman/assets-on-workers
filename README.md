# Hugo + Cloudflare Workers 🌥👷‍♀️

This is an example project showing how you can build a website with [Hugo](https://gohugo.io/), and serve it at the edge using [Cloudflare Workers](http://bit.ly/cf-workers-landing), managed via the [Serverless framework](https://serverless.com/).

[![header](./media/header.png)](http://bit.ly/cf-workers-landing)

## Why?

Cloudflare Workers allows developers to write and deploy applications at the edge. When a user requests your site, your Worker will serve it directly from one of our [175+ data centers](http://bit.ly/hugo-example-cf-network) around the world. Static sites are perfect fit for building extremely fast, high availability sites, and Cloudflare's default caching behavior will result in a high cache rate for the bulk of your site. The result is an easy-to-manage site, hosted [at the edge](http://bit.ly/hugo-example-cf-edge-server), with the ability for further customization via editing the provided [worker script](https://github.com/signalnerve/hugo-cloudflare-workers-example/blob/master/worker.js), [Serverless config file](https://github.com/signalnerve/hugo-cloudflare-workers-example/blob/master/serverless.yml), and [deploy process](https://github.com/signalnerve/hugo-cloudflare-workers-example/blob/master/bin/deploy).

## How it works

1. The website is built locally using [Hugo](https://gohugo.io/).
2. The site is built and uploaded to Google Cloud Storage - see the "Setup" section
3. A Cloudflare Worker script runs at your domain, and takes the requested path (e.g. `/about`) and requests it from your Google Cloud Storage bucket.

## Setup

Setup for this project requires some configuration at the two services we'll use to deploy and serve your site: Google Cloud Storage and Cloudflare Workers.

To start, make sure that you have `gsutil` [installed and configured](<https://cloud.google.com/storage/docs/gsutil_install>). `gsutil` is used in the deploy script for this project to sync the built version of your site with a configured storage bucket.

Next, you'll need your Cloudflare [API keys](<https://support.cloudflare.com/hc/en-us/articles/200167836-Where-do-I-find-my-Cloudflare-API-key->) and zone ID (found on the Cloudflare Dashboard for your site) in order to deploy our Worker script from the command-line.

Below is a detailed description of each key/value pair in `.env` that should be populated before deploying your site:

```
# The name of your Cloud Storage bucket
BUCKET_NAME="mybucketname"

# Your Cloudflare account id, available on the Cloudflare Dashboard
CLOUDFLARE_ACCOUNT_ID=""

# Your Cloudflare account email
CLOUDFLARE_AUTH_EMAIL=""

# Your Cloudflare auth key, available on your Cloudflare profile page
CLOUDFLARE_AUTH_KEY=""

# Your Cloudflare zone id, available on the Cloudflare Dashboard
CLOUDFLARE_ZONE_ID=""

# The route pattern for serving your Hugo site
WORKER_ROUTE="hugo-workers.signalnerve.com/*"
```

Given these keys, make a file called [`.env`](https://github.com/signalnerve/hugo-cloudflare-workers/blob/master/.env), using [`.env.example`](https://github.com/signalnerve/hugo-cloudflare-workers/blob/master/.env) as a reference:

```
cp .env.example .env
```

## Customization

This repository is intended to be forked and customized for your website. Because of this, the relevant Worker script, Serverless framework configuration, and deployment patterns are fairly open to customization in various ways:

- Changing _where_ your files are stored (see `baseUrl` in `worker.js`)
- Custom request handling based on `Content-Type` or other values: see `worker.js`

## Development

Begin by installing the dependencies for this project.

Hugo:

[Guide to installing Hugo](https://gohugo.io/getting-started/installing/).

Serverless:

```
npm install -g serverless
npm install
```

Develop your site locally:

```
hugo server -D
```

When you're ready to deploy, build and deploy to GitHub:

```
npm run deploy
```

This builds the site (`hugo`) and uploads it to your Google Cloud Storage bucket.

Deploy your worker to your Cloudflare account:

```
serverless deploy
```

Go to your configured URL to confirm that your website is being served with Cloudflare Workers!

**You only need to deploy the Worker script if you make changes to `worker.js`**: if you just want to deploy a new version of the site, `npm run deploy` will take care of building + uploading.

