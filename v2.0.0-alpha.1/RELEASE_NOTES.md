# Clustro v2.0.0-alpha.1

Released 2026-11-08 from source commit `a3dfd0d` (with uncommitted changes).

## What's new

First public build of Clustro v2, the rewrite on containerd (ADR-0015). **Alpha: for test clusters only.** It is not compatible with v1 clusters; set up v2 machines fresh.

- **Pods on containerd, no pause container.** Each pod gets its own network namespace, pinned by Clustro, and a stable IP from the node's `/24` of `10.77.0.0/16`. Containers in a pod share `localhost` and restart independently, with `restartPolicy` and backoff (10 s up to 5 min).
- **Probes:** readiness and liveness (`httpGet`, `tcpSocket`, `exec`). `Ready` and `ContainersReady` are reported per pod.
- **Secure membership:**
  - the cluster CA, TLS on the API, and an admin credential in `~/.clustro/config`;
  - join tokens (`clustroctl token create|list|revoke`), checked against `--ca-hash`;
  - mutual TLS on the agent port 7444;
  - nodes go `Ready → NotReady (20 s) → Lost (5 min)`;
  - `clustroctl node remove`.
- **Commands:** `leader`, `join --foreground`, `worker`, `apply`, `get`, `delete`, `logs` (`-f`, `--tail`, `--previous`), and `ps`, `exec`, `image import` on the node.
- **Placement:** `spec.nodeName` places a pod on a node. Other pods stay `Pending` until the scheduler (M6).

**Not yet:**
- `hostPort` and NodePorts (M4): reach a pod at its IP from its own node;
- pod traffic between nodes, WireGuard, and NAT to the internet (M4);
- Services and cluster DNS (M5);
- the scheduler, `init`/`join` as services, `reset`, `backup` (M6).

Workers need Linux with containerd 1.7+, cgroup v2 and root. macOS runs the CLI and a leader without pods.

## Status

Milestones of the v2 rewrite (docs/rewrite.md in the Clustro source):

| Milestone | Status |
|---|---|
| M1 | **Gate passed** on Linux (Amazon Linux 2023, kernel 6.18, EC2) on 2026-11-08 |
| M2 | Code complete (2026-11-08). `TestM2Gate` passes on macOS and Linux. Across 3 EC2 machines: join over TLS and the 401 for unauthenticated calls are verified; **the `NotReady → Lost → Ready` cycle across machines is still to be observed** |
| M3 | Code complete (2026-11-08). `TestM3Gate` **passes on Linux** as root against containerd 2.3.7 (EC2, Amazon Linux 2023), with `TestM1Gate` and `TestM2Gate` in the same run |
| M4–M7 | Not started |

## Install

```sh
curl -sfL https://getclustro.in/install.sh | sh                                # latest
curl -sfL https://getclustro.in/install.sh | CLUSTRO_VERSION=v2.0.0-alpha.1 sh   # this release
```

Or download a binary below and check it against `SHA256SUMS`.

## Checksums (SHA-256)

```
0bcc526d05211593c8ae18e0e87480b90c230f90f1c9ae9acc3fd46f796d78d4  clustroctl-darwin-amd64
0cc06e523c68d17d3be2fea35ffeef677e6f6c714c67334d7b63f619aacd5774  clustroctl-darwin-arm64
e9f6bc90e186801df9109341b7004b3a324c624ab7fc54e7290112a65b449399  clustroctl-darwin-universal
ee7b99ed97c2fdcb7220defb50e41ef15637c68ee8b3a5cd55dbd30ca6de9c4d  clustroctl-linux-amd64
bb87336bb2e624216f86740ecc6b89e8e611ee187ac3eaaa15bd54cf376b8957  clustroctl-linux-arm64
```
