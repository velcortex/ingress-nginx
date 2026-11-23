# Security Policy

This repository is a community fork of [kubernetes/ingress-nginx](https://github.com/kubernetes/ingress-nginx).
It is not maintained by the Kubernetes project. Please do not report issues in this fork to the Kubernetes
security team.

## Supported Versions

| Version | Supported |
| ------- | --------- |
| v1.16.x | Yes       |
| v1.15.1 and earlier (upstream releases) | No, retired upstream |

## Reporting a Vulnerability

Please report vulnerabilities privately, not in a public issue:

1. Open the [Security tab](https://github.com/velcortex/ingress-nginx/security) of this repository.
2. Click **Report a vulnerability** and describe the issue, the affected version and how to reproduce it.

Reports are handled on a best-effort basis. This fork is provided as-is, without a support or response-time
commitment.

## Scope

This fork changes the bundled components (NGINX, ModSecurity, Go, OpenSSL and the base image) and leaves the
controller code as released upstream in v1.15.1.

* A vulnerability in a bundled component that is fixed in a newer upstream release of that component is in scope:
  report it here so the component can be updated.
* A vulnerability in NGINX, ModSecurity, OpenSSL or another bundled project itself should also be reported to that
  project.

## Verifying Images

Released images are listed with their digests in the [README](README.md) and in the changelog of each release.
Pin the digest when you deploy.
