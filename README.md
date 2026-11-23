# icr.io from GitHub-hosted runners

Since the end of October 2026, the Hibernate 7 Db2 tests of [apache/grails-core](https://github.com/apache/grails-core) fail on some GitHub-hosted runners because the Docker daemon cannot pull `icr.io/db2_community/db2:11.5.9.0` from IBM's registry. Every attempt ends with Docker's 500 `request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)` or `context deadline exceeded` for `https://icr.io/v2/`. Every job that failed this way ran in the Azure region westus3 (apache/grails-core#16929).

The workflow in this repository finds out where the connection fails. Each of its 20 probe jobs records:

- the region of the runner and its public IPv4 address,
- the addresses `icr.io` resolves to,
- whether `https://icr.io/v2/` answers, and how long each phase takes, with `us.icr.io`, Docker Hub, `ghcr.io` and `quay.io` for comparison,
- whether each `icr.io` address answers on its own,
- the route to `icr.io`,
- whether the Docker CLI (`docker manifest inspect`) and the Docker daemon (`docker pull`, stopped after 60 s) get the image.

The summary of a run has one row per probe, with the region the runner names in its job log. Each probe's full output is kept as an artifact.

It runs on every push and from the Actions tab (Run workflow). The runners are free for a public repository, so it costs the ASF nothing.
