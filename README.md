Rlay is a development tool that allows you to forward HTTP calls to your local machine through a server. Since rlay's client is connecting to the server, you don't need to open any ports on your local machine to make a local webapp available to the external world. It facilitates the development of web applications when another system needs to call a webhook in your system. It is an easy to deploy, self-hosted alternative to ngrok.

It is composed of two components:

- A server, that you deploy on a machine accessible to the internet, which will receive the HTTP calls, transfer them to the client, then forward the responses.
- A client, that you run on your local machine. It will connect to a) the distant server, b) whatever app you want on your local machine, and proxy the calls made to the server to your machine.


```
┌────────────────────────┐     ┌────────────────┐     ┌──────────────────┐
│     Local machine      │     │  Relay server  │     │   Other system   │
│    ───────────────     │     │ ────────────── │     │   ─────────────  │
│                        │     │                │     │                  │
│  App ◄── Rlay client ──┼─────┼►  Rlay server ◄┼─────┼─ External system │
│                        │     │                │     │                  │
└────────────────────────┘     └────────────────┘     └──────────────────┘
```

Rlay's code is on [GitHub](https://github.com/cfe84/rlay), as well as a Kubernetes chart, [client](https://www.npmjs.com/package/rlay) and [server](https://www.npmjs.com/package/rlay-server) are available on npmjs, and a docker image is published to [dockerhub](https://hub.docker.com/repository/docker/cfe84/rlay).

# deployment

The recommended deployment is on any kubernetes server you would have dangling around. A helm chart is available in the server's directory, which contains an ingress and certificate.

# setup

On the server: deploy rlay either with docker, npm, or using kubernetes. Set the environment variable `RLAY_PASSWORD` to whatever password you want, and `RLAY_PORT` to the port to be listened. Rlay will not start if a password is not provided.

On your local machine: `npm install -g rlay`, then set an environment variable `RLAY_PASSWORD` to your rlay password, and `RLAY_HOST` to your rlay server DNS, including the protocol (e.g. https://myrlayserver.mydomain.com).

# using

- Start your local dev server, say it's a webapp listening on port 8080.
- Configure whatever remote service needs to call it to point to your rlay server (e.g. https://myrlayserver.mydomain.com)
- Start the rlay client: `rlay --port 8080`

From there on, when the remote service makes http calls, they will be forwarded to your local environment.

# deploy to Azure

Rlay can be deployed to a free Webapp on Azure.

0. Fork rlay on Github.
1. Create yourself a free Azure subscription, then a webapp using Free linux hosting
2. deploy Rlay by configuring in the App Service configuration > Deployment Center > Settings > GitHub, use the _App Service Build Provider_ as Build provider, select organization as cfe84 (or your fork), repo as _rlay_, branch as _main_, then save.
3. setup password. Go to the webapp settings > Configuration > Application Settings > add a new app setting called `RLAY_PASSWORD`, set whatever password you want.
4. activate websockets. In the same screen, click on General Settings > Web sockets > on. Click Save.
5. In TLS/SSL settings, turn on "HTTPS only"

Navigate to your web app, after a few minutes it should show `No client connected`. Install the client on your computer. Set environment variables `RLAY_PASSWORD` to the same value as the webapp, and `RLAY_HOST` to the webapp's URL. Launch rlay using `rlay --port 8080` (use a port that returns something on your machine), then refresh your webapp and see it working.

Setup a password 

<!--[![Deploy To Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fcfe84%2Frlay%2Fmaster%2Fazure-deploy%2Fazuredeploy.json) -->