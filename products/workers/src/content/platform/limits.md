---
order: 2
pcx-content-type: concept
---

# Limits

## Account plan limits

<TableWrap>

| Feature                                                                         | Free      | Paid      |
| ------------------------------------------------------------------------------- | --------- | --------- |
| [Subrequests](#subrequests)                                                     | 50        | 50        |
| [Simultaneous outgoing<br/>connections/request](#simultaneous-open-connections) | 6         | 6         |
| [Environment variables](#environment-variables)                                 | 32/worker | 32/worker |
| [Environment variable<br/>size](#environment-variables)                         | 5 KB      | 5 KB      |
| [Script size](#script-size)                                                     | 1 MB      | 1 MB      |
| [Number of scripts](#number-of-scripts)                                         | 30        | 100       |
| [Number of Cron Triggers<br/>per script](#number-of-schedules)                  | 3         | 3         |
| [Number of Cron Triggers<br/>per account](#number-of-schedules-account)         | 5         | 90        |

</TableWrap>

## Request limits

URLs have a limit of 16 KB.

Request headers observe a total limit of 32 KB, but each header is limited to 16 KB. 

Cloudflare has network-wide limits on the request body size. This limit is tied to your Cloudflare Account's plan, which is separate from your Workers plan. When the request body size of your POST/PUT/PATCH requests exceed your plan's limit, the request is rejected with a `(413) Request entity too large` error.

Cloudflare Enterprise customers may contact their account team or [Cloudflare Support](https://support.cloudflare.com/hc/articles/200172476) to request a limit beyond 500 MB.

<TableWrap>

| Cloudflare Plan | Maximum body size |
| --------------- | ----------------- |
| Free            | 100MB             |
| Pro             | 100MB             |
| Business        | 200MB             |
| Enterprise      | 500MB             |

</TableWrap>

## Response limits

Cloudflare does not enforce response limits, but cache limits for [Cloudflare's CDN are observed](https://support.cloudflare.com/hc/articles/200172516#h_51422705-42d0-450d-8eb1-5321dcadb5bc). Maximum file size is 512 MB for Free, Pro, and Business customers and 5 GB for Enterprise customers.

## Worker limits

<TableWrap>

| Feature                     | Free                                                 | Bundled Usage Model                         | Unbound Usage Model                       |
| --------------------------- | ---------------------------------------------------- | ------------------------------------------- | ----------------------------------------- |
| [Request](#request)         | 100,000&nbsp;requests/day<br/>1000&nbsp;requests/min | none                                        | none                                      |
| [Worker memory](#memory)    | 128 MB                                               | 128 MB                                      | 128 MB                                    |
| [CPU runtime](#cpu-runtime) | 10 ms                                                | 50 ms HTTP request <br/> 50 ms Cron trigger | 30 s HTTP request <br/> 15 min Cron Trigger |                                           |
| [Duration](#duration)       |                                                      |                                             | No limit* |

</TableWrap>

### Bundled Usage Model

Workers on the Bundled Usage Model are intended for use cases below 50 ms. Bundled Workers limits are based on CPU time, rather than [duration](#duration). This means that the time limit does not include the time a script is waiting for responses from network calls. The billing model for Bundled Workers is based on requests that exceed the included number of requests on the Paid plan. Learn more about [Usage Model pricing](/platform/pricing#usage-models).

<Aside type="note" header="No limit* for duration">

There is no hard limit for duration. However, after 30 seconds, there is a higher chance of eviction.

</Aside>

### Unbound Usage Model

The Workers Unbound Usage Model has a significantly higher limit than the Bundled Usage Model and is intended for use cases up to 30 seconds of CPU time for HTTP requests and up to 15 minutes of CPU time for Cron Triggers. [Duration](#duration) is not capped but after 30 seconds there is a slightly higher chance of eviction. Learn more about [Usage Model pricing](/platform/pricing#usage-models).

## KV limits

<TableWrap>

| Feature                               | Free                  | Paid       |
| ------------------------------------- | --------------------- | ---------- |
| [Reads/second](#kv)                   | 100,000 reads per day | unlimited  |
| [Writes/second (different keys)](#kv) | 1000 reads per day    | unlimited  |
| [Writes/second (same key)](#kv)       | 1                     | 1          |
| [Operations/worker invocation](#kv)   | 1000                  | 1000       |
| [Namespaces](#kv)                     | 100                   | 100        |
| [Keys/namespace](#kv)                 | 1 GB                  | unlimited  |
| [Key size](#kv)                       | 512 bytes             | 512 bytes  |
| [Key metadata](#kv)                   | 1024 bytes            | 1024 bytes |
| [Value size](#kv)                     | 25 MiB                | 25 MiB     |

</TableWrap>

<Aside type="note" header="Free vs. Paid plan pricing">

Refer to [KV pricing](/platform/pricing#workers-kv) to review the specific KV operations you are allowed under each plan with their pricing.

</Aside>

## Cache API limits

<TableWrap>

| Feature                               | Free   | Bundled |
| ------------------------------------- | ------ | ------- |
| [Max object size](#cache-api)         | 512 MB | 512 MB  |
| [Calls/request](#cache-api)           | 50     | 50      |
| [Storage/request](#cache-api)         | 5 GB   | 5 GB    |

</TableWrap>

## Durable Objects limits

<TableWrap>

| Feature                                 | Limit
| --------------------------------------- | ------
| [Number of objects](#durable-objects)   | unlimited
| [Storage per account](#durable-objects) | 10 GB (can be raised by contacting Cloudflare)
| [Storage per class](#durable-objects)   | unlimited
| [Storage per object](#durable-objects)  | unlimited
| [Key size](#durable-objects)            | 2048 bytes
| [Value size](#durable-objects)          | 32 KiB
| [CPU per request](#durable-objects)     | 30s

</TableWrap>

---

## Request

Bundled (Paid) Workers scripts automatically scale onto thousands of Cloudflare edge servers around the world. There is no general limit to the number of requests per second Workers can handle.

Cloudflare’s abuse protection methods do not affect well-intentioned traffic. However, if you send many thousands of requests per second from a small number of client IP addresses, you can inadvertently trigger Cloudflare’s abuse protection. If you expect to receive `1015` errors in response to traffic or expect your application to incur these errors, contact your Cloudflare account team to increase your limit.

The burst rate and daily request limits apply at the account level, meaning that requests on your `*.workers.dev` subdomain count toward the same limit as your zones. Upgrade to a [Paid plan](https://dash.cloudflare.com/?account=workers/plans) to automatically lift these limits.

### Burst rate

Accounts using the Workers Free plan are subject to a burst rate limit of 1000 requests per minute. Users visiting a rate limited site will receive a Cloudflare `1015` error page. However if you are calling your script programmatically, you can detect the rate limit page and handle it yourself by looking for HTTP status code `429`.

### Daily request

Accounts using the Workers Free plan are subject to a daily request limit of 100,000 requests. Free plan daily requests counts reset at midnight UTC. A Worker that fails as a result of daily request limit errors can be configured by toggling its corresponding [route](/platform/routes) in two modes: 1) Fail open and 2) Fail closed.

#### Fail open

Routes in fail open mode will bypass the failing Worker and prevent it from operating on incoming traffic. Incoming requests will behave as if there was no Worker.

#### Fail closed

Routes in fail closed mode will display a Cloudflare `1027` error page to visitors, signifying the Worker has been temporarily disabled. Cloudflare recommends this option if your Worker is performing security related tasks.

---

## Memory

Only one Workers instance runs on each of the many global Cloudflare network edge servers. Each Workers instance can consume up to 128 MB of memory. Use [global variables](/runtime-apis/web-standards) to persist data between requests on individual nodes; note however, that nodes are occasionally evicted from memory.

If a Worker processes a request that pushes the Worker over the 128MB limit, the Cloudflare Workers runtime may cancel one or more requests. To view these errors, as well as CPU limit overages, go to [**Workers**](https://dash.cloudflare.com/?to=/:account/workers) on the Cloudflare dashboard > **Manage Workers** > select the Worker you would like to investigate > scroll down to **Invocation Statuses** and examine *Exceeded Resources*. 

Use the [TransformStream API](/runtime-apis/streams/transformstream) to stream responses if you are concerned about memory usage. This avoids loading an entire response into memory.

---

## CPU runtime

Most Workers requests consume less than a millisecond. It is rare to find a normally operating Workers script that exceeds the CPU time limit. A Worker may consume up to 10 ms on the Free plan and up to 50 ms for Bundled Workers on the Paid Plan. The Paid Plan also offers up to a 30 second [duration](/platform/limits#duration) for increased compute time. The 10 ms allowance on the Free plan is enough execution time for most use cases including application hosting.

There is no limit on the real runtime for a Workers script. As long as the client that sent the request remains connected, the Workers script can continue processing, making subrequests, and setting timeouts on behalf of that request. When the client disconnects, all tasks associated with that client request are canceled. You can use [`event.waitUntil()`](/runtime-apis/fetch-event) to delay cancellation for another 30 seconds or until the promise passed to `waitUntil()` completes.

---

## Duration

Duration is the measurement of wall-clock time. This is measured in Gigabyte-seconds (GB-s). When a Worker is executed, it is allocated 128 MB of [memory](/platform/limits#memory). As the Worker continues to execute that memory remains allocated, even during network IO requests.

For example, when a Worker executes via a [scheduled event](/runtime-apis/scheduled-event), it executes for four seconds, including network-bound IO time: `4s x 0.125GB (or 128Mb) = .5 GB-s`.

Duration is most applicable to Unbound Workers on the [Paid plan](/platform/pricing#paid-plan) and [Durable Objects](/learning/using-durable-objects).

---

## Subrequests

### Can a Workers script make subrequests to load other sites on the Internet?

Yes. Use the [Fetch API](/runtime-apis/fetch) to make arbitrary requests to other Internet resources.

### How many subrequests can I make?

The limit for subrequests a Workers script can make is 50 per request. Each subrequest in a redirect chain counts against this limit. This means that the number of subrequests a Workers script makes could be greater than the number of `fetch(request)` calls in the script.

### How long can a subrequest take?

There is no set limit on the amount of real time a Worker may use. As long as the client which sent a request remains connected, the Worker may continue processing, making subrequests, and setting timeouts on behalf of that request.

When the client disconnects, all tasks associated with that client’s request are proactively canceled. If the Worker passed a promise to [`event.waitUntil()`](/runtime-apis/fetch-event), cancellation will be delayed until the promise has completed or until an additional 30 seconds have elapsed, whichever happens first.

---

## Simultaneous open connections

While handling a request, each Worker script is allowed to have up to six connections open simultaneously. The connections opened by the following API calls all count toward this limit:

- the `fetch()` method of the [Fetch API](/runtime-apis/fetch).
- `get()`, `put()`, `list()`, and `delete()` methods of [Workers KV namespace objects](/runtime-apis/kv).
- `put()`, `match()`, and `delete()` methods of [Cache objects](/runtime-apis/cache).

Once a Worker has six connections open, it can still attempt to open additional connections. However, these attempts are put in a pending queue — the connections will not be initiated until one of the currently open connections has closed. Since earlier connections can delay later ones, if a Worker tries to make many simultaneous subrequests, its later subrequests may appear to take longer to start.

If the system detects that a Worker is deadlocked on open connections — for example, if the Worker has pending connection attempts but has no in-progress reads or writes on the connections that it already has open — then the least-recently-used open connection will be canceled to unblock the Worker. If the Worker later attempts to use a canceled connection, an exception will be thrown. These exceptions should rarely occur in practice, though, since it is uncommon for a Worker to open a connection that it does not have an immediate use for.

---

## Environment variables

The maximum number of environment variables (secret and text combined) for a Worker is 32 variables.
There is no limit to the number of environment variables per account.

Each environment variable has a size limitation of 5 KB.

### Script size

A Workers script can be up to 1 MB in size after compression.

### Number of scripts

Unless otherwise negotiated as a part of an enterprise level contract, all Workers accounts are limited to a maximum of 30 scripts at any given time.

<Aside type="note">

App Workers scripts do not count towards this limit.

</Aside>

---

## KV

Workers KV supports:

- Up to 100 Namespaces per account
- Unlimited keys per namespace
- Keys of up to 512 bytes
- Values of up to 25 MiB
- Metadata of up to 1024 bytes per key
- Unlimited reads per second
- Unlimited writes per second, if they are to different keys
- Up to one write per second to any particular key

Workers KV read performance is determined by the amount of read-volume a given key receives. Maximum performance for a key is not reached unless that key is being read at least a couple times per minute in any given data center.

Workers KV is an eventually consistent system, meaning that reads will sometimes reflect an older state of the system. While writes will often be visible globally immediately, it can take up to 60 seconds before reads in all edge locations are guaranteed to see the new value.

---

## Cache API

- 50 total `put()`, `match()`, or `delete()` calls per-request, using the same quota as `fetch()`

- 5 GBs total `put()` per-request

<Aside type="note">

The size of chunked response bodies (`Transfer-Encoding: chunked`) is not known in advance. Then, `.put()`ing such responses will block subsequent `.put()`s from starting until the current `.put()` completes.

</Aside>

---

## Durable Objects

- Unlimited Durable Objects within an account or of a given class

- 10 GB total storage per account (can be raised by contacting Cloudflare)

- No storage limit per Durable Object separate from the account limit

- No storage limit per Durable Object class separate from the account limit

- Storage keys of up to 2 KiB (2048 bytes)

- Storage values of up to 32 KiB (32768 bytes)

- 30s of CPU time per request, including websocket messages

Durable Objects scale well across Objects, but each object is inherently single-threaded.  A baseline of 100 req/sec is a good floor estimate of the request rate an individual Object can handle, though this will vary with workload. 

Durable Objects have been built such that the number of objects in the system do not need to be limited. You can create and run as many separate objects as you want. The main limit to your usage of Durable Objects is the total storage limit per account - if you need more storage, contact your account team.
