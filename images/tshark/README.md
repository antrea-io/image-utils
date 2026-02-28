# antrea/tshark

This Docker image is based on Ubuntu and includes
[tshark](https://www.wireshark.org/docs/man-pages/tshark.html), the
terminal-based network protocol analyzer from the Wireshark suite.

The antrea/tshark image is built and pushed to the registry every time this
directory is updated in the main branch. The following tags are produced for
each build:

* `<semver>-<short-sha>` (e.g. `4.2.2-abc1234`): immutable tag identifying the
  exact build.
* `<semver>` (e.g. `4.2.2`): rolling tag, always points to the latest build for
  that tshark version.
* `latest`: rolling tag, always points to the most recent build.

## Running as a DaemonSet on Kubernetes

A common use case is to run tshark on every node of a cluster to capture
traffic on the host network. The example below deploys tshark as a DaemonSet
in `hostNetwork` mode. The `NET_RAW` and `NET_ADMIN` capabilities are required
for packet capture.

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: tshark
spec:
  selector:
    matchLabels:
      app: tshark
  template:
    metadata:
      labels:
        app: tshark
    spec:
      hostNetwork: true
      containers:
      - name: tshark
        image: antrea/tshark:latest
        imagePullPolicy: IfNotPresent
        command: ["tshark"]
        args: ["-i", "any"]
        securityContext:
          capabilities:
            add:
            - NET_RAW
            - NET_ADMIN
```

Note that `allowPrivilegeEscalation: false` must **not** be set in the
`securityContext`. Even with `NET_RAW` and `NET_ADMIN` granted, setting
`allowPrivilegeEscalation: false` (which sets `no_new_privs` on the process)
prevents the capabilities from being promoted into `dumpcap`'s effective set
when tshark invokes it as a child process, causing captures to fail.
