# Changelog

## [v2.0.0-alpha.3](https://github.com/Try-Clustro/release/releases/tag/v2.0.0-alpha.3) — 2026-11-12

See [v2.0.0-alpha.3/RELEASE_NOTES.md](v2.0.0-alpha.3/RELEASE_NOTES.md).

## [v2.0.0-alpha.1](https://github.com/Try-Clustro/release/releases/tag/v2.0.0-alpha.1) — 2026-11-08

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

