# Clustro v2.0.0-alpha.2

Released 2026-11-10 from source commit `unknown`.

## What's new

- Scheduler: pods without spec.nodeName are placed automatically (resources, hostPort, cordon, affinity, round-robin)
- hostPort: <node-ip>:<hostPort> forwards to the pod
- Pods can reach the internet
## Status

Milestones of the v2 rewrite (docs/rewrite.md in the Clustro source):

| Milestone | Status |
|---|---|
