---
title: Embedded Weaviate
sidebar_position: 4
image: og/docs/installation.jpg
# tags: ['installation', 'embedded', 'client']
---
## Overview

Embedded Weaviate is a new deployment model, which allows you to start a Weaviate instance, straight in your application code using a Weaviate Client library.

:::caution Experimental
Embedded Weaviate is still in the **Experimental** phase.

Some of the APIs and parameters might change over time, as we work towards a perfect implementation.
:::

### How is this possible?

With every Weaviate [release](https://github.com/weaviate/weaviate/releases) we also publish executable Linux binaries ([see assets](https://github.com/weaviate/weaviate/releases)).

This allows launching the Weaviate database server from the client instantiation call, which makes the "installation" step transparent in the background:

import EmbeddedInstantiation from '/_includes/code/embedded.instantiate.mdx';

<EmbeddedInstantiation />

## Embedded options

The Weaviate server spwaned from the client can be configured via parameters passed at instantiation time, and via environment variables. All parameters are optional.

| Parameter | Type | Description | Default value |
| --------- | ---- | ----------- | ------------- |
| persistence_data_path | string | Directory where the files making up the database are stored. | When the `XDG_DATA_HOME` env variable is set, the default value is:<br/>`XDG_DATA_HOME/weaviate/`<br/><br/>Otherwise it is:<br/>`~/.local/share/weaviate` |
| binary_path | string | Directory where to download the binary. If deleted, the client will download the binary again. | When the `XDG_CACHE_HOME` env variable is set, the default value is:<br/>`XDG_CACHE_HOME/weaviate-embedded/`<br/><br/>Otherwise it is:<br/>`~/.cache/weaviate-embedded` |
| version | string | Version takes two types of input:<br/>- **version number** - for example `"1.18.3"` or `"latest"`<br/>- **full URL** pointing to a Linux AMD64 or ARM64 binary | Latest stable version |
| port | integer | Which port the Weaviate server will listen to. Useful when running multiple instances in parallel. | 6666 |
| hostname | string | Hostname/IP to bind to. | 127.0.0.1 |
| additional_env_vars | key: value | Useful to pass additional environment variables to the server, such as API keys. |

:::danger XDG Base Directory standard values
It is **not recommended** to modify the `XDG_DATA_HOME` and `XDG_CACHE_HOME` environment variables from the standard **XDG Base Directory** values, as that might affect many other (non-Weaviate related) applications and services running on the same server.
:::

:::tip Providing the Weaviate version as a URL
To find the **full URL** for `version`:
* head to [Weaviate releases](https://github.com/weaviate/weaviate/releases),
* find the **Assets** section for the required Weaviate version
* and copy the link to required `(name).tar.gz` file.

For example, here is the URL of the Weaviate `1.18.2` `AMD64` binary: `https://github.com/weaviate/weaviate/releases/download/v1.18.2/weaviate-v1.18.2-linux-amd64.tar.gz`.
:::

## Starting Embedded Weaviate under the hood

Here's what happens behind the scenes when the client uses the embedded options in the instantiation call:
1. The client downloads a Weaviate release from GitHub and caches it
2. It then spawns a Weaviate process with a data directory configured to a specific location, and listening to the specified port (by default 6666)
3. The server's STDOUT and STDERR are piped to the client
4. The client connects to this server process (e.g. to `http://127.0.0.1:6666`) and runs the client code
5. After running the code (when the application terminates), the client shuts down the Weaviate process
6. The data directory is preserved, so subsequent invocations have access to the data from all previous invocations, across all clients using the embedded option.

### Lifecycle

The embedded instance will stay alive for as long as the parent application is running.

When the application exits (e.g. due to an exception or by reaching the end of the script), Weaviate will shut down the embedded instance, but the data will persist.

:::note Embedded with Jupyter Notebooks
An Embedded instance will stay alive for as long as the Jupyter notebook is active.

This is really useful, as it will let you experiment and work with your Weaviate projects and examples.
:::

## Supported Environments

### Operating Systems

Embedded Weaviate is currently supported on Linux only.

We are actively working to provide support for MacOS. We hope to share an update in the near future.

### Language Clients

Embedded Weaviate is supported in the following language clients:

* [Python client](../client-libraries/python.md) – `v3.15.4` or newer
* [TypeScript client](https://github.com/weaviate/typescript-client) – `v1.0.0` or newer

## More Resources

import DocsMoreResources from '/_includes/more-resources-docs.md';

<DocsMoreResources />
