<p align="center">
  <a href="https://codesandbox.io">
    <img src="https://codesandbox.io/static/img/banner.png?v=2" height="300px">
  </a>
</p>

&nbsp;

[![All Contributors](https://img.shields.io/badge/all_contributors-153-orange.svg?style=flat-square)](#contributors-)
[![CircleCI](https://circleci.com/gh/codesandbox/codesandbox-client.svg?style=svg)](https://circleci.com/gh/codesandbox/codesandbox-client)
[![BrowserStack Status](https://www.browserstack.com/automate/badge.svg?badge_key=cVJuczlJWUtqWXhIbFN1ZjVQekF4NzNsd3phNEZRaGlWU0pHYVVkdGRFWT0tLXFtTVhaOWRySmN0ZG5QVDNDQ0g5Z0E9PQ==--79fe3eae4f149a400d396c9b12d3988f685785cf)](https://www.browserstack.com/automate/public-build/cVJuczlJWUtqWXhIbFN1ZjVQekF4NzNsd3phNEZRaGlWU0pHYVVkdGRFWT0tLXFtTVhaOWRySmN0ZG5QVDNDQ0g5Z0E9PQ==--79fe3eae4f149a400d396c9b12d3988f685785cf)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)
[![first-timers-only Friendly](https://img.shields.io/badge/first--timers--only-friendly-blue.svg)](http://www.firsttimersonly.com/)
[![lerna](https://img.shields.io/badge/maintained%20with-lerna-cc00ff.svg)](https://lerna.js.org/)

An instantly ready, full-featured online IDE for web development on any device
with a browser. Enabling you to start new projects quickly and prototype
rapidly. With CodeSandbox, you can create web apps, experiment with code, test
ideas, and share creations easily.

## Locally Hosting 

if you want to host the bundler yourself, you will need to do a few things.

The bundler is part of the codesandbox-client codebase: https://github.com/codesandbox/codesandbox-client

Clone the codesandbox-client and install the dependencies in the root folder (yarn install).

yarn build:deps to build some of the packages lerna needs for internal links.

create your instance of sandpack with yarn build:sandpack.

This creates a www folder in the root of codesandbox-client. That www folder is the sandpack folder sandpack-client connects to on {version}-sandpack.codesandbox.io. Once you have this hosted on your end you can pass bundlerURL when calling:

```
new SandpackClient(iframe, sandboxInfo, {
  bundlerURL: "https://your-hosted-version",
});
```

or, if you use sandpack-react, you can bundlerURL in the options of the Sandpack preset.
Why

There are few reasons for hosting the bundler like this, as opposed to having it exported as library code.
Security

The bundler evaluates and transpiles all files in an iframe under a different subdomain. This is important, because it prevents attackers from tampering with cookies of the host domain when evaluating code.
Performance

We heavily make use of Web Workers for transpilations. Almost all our transpilation happens in web workers, and there is no easy way yet to bundle this in a library.
Bundle Size

Another reason to host the bundler externally is because of code splitting: we split all our transpilers away and load them on-demand. If a user doesn't use sass we won't load the transpiler. This wouldn't be possible if we would give one big JS file as the library.
Offline Support

We use Service Workers to download all transpilers in the background, so the next time a user visits your website they don't have to download the bundler anymore and it can be used offline. This is possible because we host the service worker externally.
Self-host private packages

The custom private NPM registry allows Sandpack instances to retrieve private NPM packages from your own registry. This option requires running a third service (Node.js server) and configuring your Sandpack provider to consume these dependencies from another registry, not the public ones.

You'll need:

    Host a Node.js server, which will run registry proxy;
    GitHub/NPM authentication token with read access;

Self-host the proxy

We recommend hosting a service that allows you to proxy your private packages from a registry (GitHub/Npm/your own) to a new one, which would make the packages available through another URL. As Sandpack bundles everything in-browser, it needs to find a way to connect to the registry which provides the project dependencies. First, Sandpack will try to fetch all dependencies from public registries, for example, react or redux. Then you can let Sandpack know which dependencies (or scoped dependencies) should be fetched from a different registry. For example, your custom registry.
Our recommendation

Suppose you don't already have a public registry, we recommend using Verdaccio. An open-source project that creates a private registry and can proxy other registries, such as GitHub and Npm. You can find examples of how to use the examples folder in the main repository.
Sandpack configuration

Once the proxy is running and configured, you need to set some options in your Sandpack context:

```
<Sandpack
  customSetup={{
    dependencies: { "@codesandbox/test-package": "1.0.5" },
    npmRegistries: [
      {
        enabledScopes: ["@codesandbox"],
        limitToScopes: true,
        registryUrl: "PROXY_URL",
      },
    ],
  }}
  files={{
    "/App.js": `import { Button } from "@codesandbox/test-package"
export default function App() {
  return (
    <div>
      <Button>I'm a private Package</Button>
    </div>
  )
}
`,
  }}
  template="react"
/>
```

Security

It's essential to keep the information and tokens of the npm registry private! By using this method, it's best to keep in mind that it could expose all private packages in your account. Be careful where and how this proxy will be used. Make sure to use authentication tokens with read-only access.

It's also possible to expose only specific packages. If the custom scopes are @scope/package-name instead of @scope/*, it will only expose that particular package. You can even do something like @scope/design-system* to expose all packages of the design system.

------------------------------------

## Other CodeSandbox repositories

CodeSandbox consists of several separate servers, some of which are open
sourced.

- Client: the web application
- Server: the [Phoenix](https://github.com/phoenixframework/phoenix) API server
- Nginx: Nginx config files
- [Git Extractor](https://github.com/codesandbox/codesandbox-importers):
  responsible for extracting the source from a GitHub repository
- [CLI](https://github.com/codesandbox/codesandbox-importers/tree/master/packages/cli):
  the CLI to upload a CodeSandbox project from your command line

## Documentation

You can find our documentation on our
[website](https://codesandbox.io/docs/learn/introduction/overview)


