# Community fork: Ingress NGINX with security updates

This repository is a community fork of [kubernetes/ingress-nginx](https://github.com/kubernetes/ingress-nginx),
which was retired in March 2026 and no longer receives security fixes. It continues from the last upstream
release, `controller-v1.15.1`, and updates the bundled components to clear known vulnerabilities.

The controller code, command line flags, annotations, IngressClass and Helm chart values are unchanged, so it is a
drop-in replacement for the upstream `v1.15.1` controller image.

| Component | Upstream v1.15.1 | This fork v1.16.0 |
|---|---|---|
| NGINX | 1.27.1 | 1.30.7 |
| ModSecurity (libmodsecurity) | 3.0.14 | 3.0.17 |
| Go | 1.26.1 | 1.26.11 |
| OpenSSL | 3.5.x (Alpine 3.23) | 3.5.9-r1 |
| Lua modules / LuaJIT | OpenResty 1.27 set | OpenResty 1.29.8.1 set (required by NGINX 1.30) |
| Alpine | 3.23.3 | 3.23.8 |

## Security fixes in v1.16.0

* **NGINX "NGINX2Root" (November 2026):** CVE-2026-123007, CVE-2026-123633, CVE-2026-123634, CVE-2026-121810 and
  CVE-2026-118899, fixed by moving to NGINX 1.30.7. CVE-2026-113425 (mail proxy) does not apply, the mail module is
  not built.
* **Earlier NGINX vulnerabilities** fixed between 1.27.2 and 1.30.7, for example CVE-2026-42945, CVE-2026-9256,
  CVE-2026-42055, CVE-2026-42533, CVE-2026-97205, CVE-2026-56434, CVE-2026-97629, CVE-2026-1642 and CVE-2026-60005.
* **ModSecurity:** CVE-2026-42268, CVE-2026-30923, CVE-2026-52747, CVE-2026-73856, CVE-2026-73857 and
  CVE-2026-104269, fixed by moving to libmodsecurity 3.0.17.
* **Go standard library and modules:** fixed by building with Go 1.26.11 and updated dependencies.
* **OpenSSL:** CVE-2026-88822, CVE-2026-88823, CVE-2026-95691, CVE-2026-95692 and CVE-2026-107496, fixed in 3.5.9-r1.

NGINX is upgraded to a fixed release rather than patched in place, so vulnerability scanners that read the NGINX
version from the binary report the fixed version. The full list is in
[changelog/controller-1.16.0.md](changelog/controller-1.16.0.md).

### Scanner results at release

| Scanner | Result |
|---|---|
| Trivy | No known vulnerabilities reported |
| Grype | None reported against NGINX. 40 findings remain on Alpine packages: OpenSSL 3.5.9-r1 (`openssl`, `libssl3`, `libcrypto3`), `busybox` (CVE-2025-60876) and `nghttp2-libs` (CVE-2026-58055), with no fixed package version listed |

Some scanners also report open advisories for the Alpine 3.23 packages `libxml2` (2.13.9-r1) and `protobuf` (31.1-r1).
These are the newest versions available in Alpine 3.23 at the time of this release.

Results depend on the scanner and its database date. Scan the image yourself before you rely on it.

## Images

| Image | Reference |
|---|---|
| Controller | `ghcr.io/velcortex/ingress-nginx/controller:v1.16.0@sha256:3f11293584b8b8c0ffc7dc2e419278645097a036414d0bad4e821a7679f1a679` |
| NGINX base (build time only) | `ghcr.io/velcortex/ingress-nginx/nginx:v2.4.0@sha256:a264ea37e70e3dcc1086e624d999c930c4b36604cbbabb85af6d8d8dd07c5a20` |

Images are built for `linux/amd64`.

## Use it with the upstream Helm chart

Only the controller image changes. Keep your existing values and override the image:

```yaml
controller:
  image:
    registry: ghcr.io/velcortex
    image: ingress-nginx/controller
    tag: v1.16.0
    digest: sha256:3f11293584b8b8c0ffc7dc2e419278645097a036414d0bad4e821a7679f1a679
```

The chart ships with the digest of the upstream image, so `digest` must be set as above (or to an empty string).

## Build it yourself

```console
# NGINX base image (optional, the published base is referenced in NGINX_BASE)
docker build -t <registry>/ingress-nginx/nginx:v2.4.0 images/nginx/rootfs

# Controller image
make build image ARCH=amd64 REGISTRY=<registry>/ingress-nginx TAG=v1.16.0
```

This fork is provided as-is under the Apache-2.0 license, without any warranty or support commitment. Test it in
your own environment before using it in production. Ingress NGINX remains a retired project upstream.

# Ingress NGINX Retirement

## Retiring

[What You Need to Know about Ingress NGINX Retirement](https://www.kubernetes.io/blog/2025/11/11/ingress-nginx-retirement/):

* Best-effort maintenance will continue until March 2026.
* Afterward, there will be no further releases, no bugfixes, and no updates to resolve any security vulnerabilities that may be discovered.
* Existing deployments of Ingress NGINX will not be broken.
  * Existing project artifacts such as Helm charts and container images will remain available.

# Ingress NGINX Controller

[![CII Best Practices](https://bestpractices.coreinfrastructure.org/projects/5691/badge)](https://bestpractices.coreinfrastructure.org/projects/5691)
[![Go Report Card](https://goreportcard.com/badge/github.com/kubernetes/ingress-nginx)](https://goreportcard.com/report/github.com/kubernetes/ingress-nginx)
[![GitHub license](https://img.shields.io/github/license/kubernetes/ingress-nginx.svg)](https://github.com/kubernetes/ingress-nginx/blob/main/LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/kubernetes/ingress-nginx.svg)](https://github.com/kubernetes/ingress-nginx/stargazers)

## Overview

ingress-nginx was an Ingress controller for Kubernetes using [NGINX](https://www.nginx.org/) as a reverse proxy and load
balancer.

[Learn more about Ingress on the Kubernetes documentation site](https://kubernetes.io/docs/concepts/services-networking/ingress/).

## Usage warnings

If you are not already using ingress-nginx, you should not be deploying it as it is [not being developed](#retiring). Instead you should identify a [Gateway API](https://gateway-api.sigs.k8s.io/guides/) implementation and use it.

Do not use in multi-tenant Kubernetes production installations. This project assumes that users that can create Ingress objects are administrators of the cluster. See the [FAQ](https://kubernetes.github.io/ingress-nginx/faq/#faq) for more.

## Troubleshooting

If you encounter issues, review the [troubleshooting docs](docs/troubleshooting.md),
[search for an issue](https://github.com/kubernetes/ingress-nginx/issues), or talk to us on the
[#ingress-nginx-users channel](https://kubernetes.slack.com/messages/ingress-nginx-users) on the Kubernetes Slack server.

## Changelog

See [the list of releases](https://github.com/kubernetes/ingress-nginx/releases) for all changes.
For detailed changes for each release, please check the [changelog-$version.md](./changelog) file for the release version.
For detailed changes on the `ingress-nginx` helm chart, please check the changelog folder for a specific version.
[CHANGELOG-$current-version.md](./charts/ingress-nginx/changelog) file.

### Supported Versions table

Supported versions for the ingress-nginx project mean that we have completed E2E tests, and they are passing for
the versions listed. Ingress-Nginx versions **may** work on older versions, but the project does not make that guarantee.

For v1.16.0 of this fork, the E2E suite was run on Kubernetes 1.35 and a functional and load check on Kubernetes 1.33. The controller code is unchanged from v1.15.1, so the
other Kubernetes versions are carried over from that release and were not re-tested. No new Helm chart is published;
use chart 4.15.1 with the image override described at the top of this page.

| Supported | Ingress-NGINX version | k8s supported version         | Alpine Version | Nginx Version | Helm Chart Version |
| :-------: | --------------------- | ----------------------------- | -------------- | ------------- | ------------------ |
|    🔄     | **v1.16.0** (this fork) | 1.35, 1.34, 1.33, 1.32, 1.31  | 3.23.8         | 1.30.7        | 4.15.1 (image override) |
|    🔄     | **v1.15.1**           | 1.35, 1.34, 1.33, 1.32, 1.31  | 3.23.3         | 1.27.1        | 4.15.1             |
|    🔄     | **v1.15.0**           | 1.35, 1.34, 1.33, 1.32, 1.31  | 3.23.3         | 1.27.1        | 4.15.0             |
|    🔄     | **v1.14.4**           | 1.34, 1.33, 1.32, 1.31, 1.30  | 3.23.3         | 1.27.1        | 4.14.4             |
|    🔄     | **v1.14.3**           | 1.34, 1.33, 1.32, 1.31, 1.30  | 3.23.2         | 1.27.1        | 4.14.3             |
|    🔄     | **v1.14.2**           | 1.34, 1.33, 1.32, 1.31, 1.30  | 3.23.2         | 1.27.1        | 4.14.2             |
|    🔄     | **v1.14.1**           | 1.34, 1.33, 1.32, 1.31, 1.30  | 3.22.2         | 1.27.1        | 4.14.1             |
|    🔄     | **v1.14.0**           | 1.34, 1.33, 1.32, 1.31, 1.30  | 3.22.2         | 1.27.1        | 4.14.0             |
|    🔄     | **v1.13.8**           | 1.33, 1.32, 1.31, 1.30, 1.29  | 3.23.3         | 1.27.1        | 4.13.8             |
|    🔄     | **v1.13.7**           | 1.33, 1.32, 1.31, 1.30, 1.29  | 3.23.2         | 1.27.1        | 4.13.7             |
|    🔄     | **v1.13.6**           | 1.33, 1.32, 1.31, 1.30, 1.29  | 3.23.2         | 1.27.1        | 4.13.6             |
|    🔄     | **v1.13.5**           | 1.33, 1.32, 1.31, 1.30, 1.29  | 3.22.2         | 1.27.1        | 4.13.5             |
|    🔄     | **v1.13.4**           | 1.33, 1.32, 1.31, 1.30, 1.29  | 3.22.2         | 1.27.1        | 4.13.4             |
|    🔄     | **v1.13.3**           | 1.33, 1.32, 1.31, 1.30, 1.29  | 3.22.1         | 1.27.1        | 4.13.3             |
|    🔄     | **v1.13.2**           | 1.33, 1.32, 1.31, 1.30, 1.29  | 3.22.1         | 1.27.1        | 4.13.2             |
|    🔄     | **v1.13.1**           | 1.33, 1.32, 1.31, 1.30, 1.29  | 3.22.1         | 1.27.1        | 4.13.1             |
|    🔄     | **v1.13.0**           | 1.33, 1.32, 1.31, 1.30, 1.29  | 3.22.0         | 1.27.1        | 4.13.0             |
|           | v1.12.8               | 1.32, 1.31, 1.30, 1.29, 1.28  | 3.22.2         | 1.25.5        | 4.12.8             |
|           | v1.12.7               | 1.32, 1.31, 1.30, 1.29, 1.28  | 3.22.1         | 1.25.5        | 4.12.7             |
|           | v1.12.6               | 1.32, 1.31, 1.30, 1.29, 1.28  | 3.22.1         | 1.25.5        | 4.12.6             |
|           | v1.12.5               | 1.32, 1.31, 1.30, 1.29, 1.28  | 3.22.1         | 1.25.5        | 4.12.5             |
|           | v1.12.4               | 1.32, 1.31, 1.30, 1.29, 1.28  | 3.22.0         | 1.25.5        | 4.12.4             |
|           | v1.12.3               | 1.32, 1.31, 1.30, 1.29, 1.28  | 3.21.3         | 1.25.5        | 4.12.3             |
|           | v1.12.2               | 1.32, 1.31, 1.30, 1.29, 1.28  | 3.21.3         | 1.25.5        | 4.12.2             |
|           | v1.12.1               | 1.32, 1.31, 1.30, 1.29, 1.28  | 3.21.3         | 1.25.5        | 4.12.1             |
|           | v1.12.0               | 1.32, 1.31, 1.30, 1.29, 1.28  | 3.21.0         | 1.25.5        | 4.12.0             |
|           | v1.12.0-beta.0        | 1.32, 1.31, 1.30, 1.29, 1.28  | 3.20.3         | 1.25.5        | 4.12.0-beta.0      |
|           | v1.11.8               | 1.30, 1.29, 1.28, 1.27, 1.26  | 3.22.0         | 1.25.5        | 4.11.8             |
|           | v1.11.7               | 1.30, 1.29, 1.28, 1.27, 1.26  | 3.21.3         | 1.25.5        | 4.11.7             |
|           | v1.11.6               | 1.30, 1.29, 1.28, 1.27, 1.26  | 3.21.3         | 1.25.5        | 4.11.6             |
|           | v1.11.5               | 1.30, 1.29, 1.28, 1.27, 1.26  | 3.21.3         | 1.25.5        | 4.11.5             |
|           | v1.11.4               | 1.30, 1.29, 1.28, 1.27, 1.26  | 3.21.0         | 1.25.5        | 4.11.4             |
|           | v1.11.3               | 1.30, 1.29, 1.28, 1.27, 1.26  | 3.20.3         | 1.25.5        | 4.11.3             |
|           | v1.11.2               | 1.30, 1.29, 1.28, 1.27, 1.26  | 3.20.0         | 1.25.5        | 4.11.2             |
|           | v1.11.1               | 1.30, 1.29, 1.28, 1.27, 1.26  | 3.20.0         | 1.25.5        | 4.11.1             |
|           | v1.11.0               | 1.30, 1.29, 1.28, 1.27, 1.26  | 3.20.0         | 1.25.5        | 4.11.0             |
|           | v1.10.6               | 1.30, 1.29, 1.28, 1.27, 1.26  | 3.21.0         | 1.25.5        | 4.10.6             |
|           | v1.10.5               | 1.30, 1.29, 1.28, 1.27, 1.26  | 3.20.3         | 1.25.5        | 4.10.5             |
|           | v1.10.4               | 1.30, 1.29, 1.28, 1.27, 1.26  | 3.20.0         | 1.25.5        | 4.10.4             |
|           | v1.10.3               | 1.30, 1.29, 1.28, 1.27, 1.26  | 3.20.0         | 1.25.5        | 4.10.3             |
|           | v1.10.2               | 1.30, 1.29, 1.28, 1.27, 1.26  | 3.20.0         | 1.25.5        | 4.10.2             |
|           | v1.10.1               | 1.30, 1.29, 1.28, 1.27, 1.26  | 3.19.1         | 1.25.3        | 4.10.1             |
|           | v1.10.0               | 1.29, 1.28, 1.27, 1.26        | 3.19.1         | 1.25.3        | 4.10.0             |
|           | v1.9.6                | 1.29, 1.28, 1.27, 1.26, 1.25  | 3.19.0         | 1.21.6        | 4.9.1              |
|           | v1.9.5                | 1.28, 1.27, 1.26, 1.25        | 3.18.4         | 1.21.6        | 4.9.0              |
|           | v1.9.4                | 1.28, 1.27, 1.26, 1.25        | 3.18.4         | 1.21.6        | 4.8.3              |
|           | v1.9.3                | 1.28, 1.27, 1.26, 1.25        | 3.18.4         | 1.21.6        | 4.8.*              |
|           | v1.9.1                | 1.28, 1.27, 1.26, 1.25        | 3.18.4         | 1.21.6        | 4.8.*              |
|           | v1.9.0                | 1.28, 1.27, 1.26, 1.25        | 3.18.2         | 1.21.6        | 4.8.*              |
|           | v1.8.4                | 1.27, 1.26, 1.25, 1.24        | 3.18.2         | 1.21.6        | 4.7.*              |
|           | v1.7.1                | 1.27, 1.26, 1.25, 1.24        | 3.17.2         | 1.21.6        | 4.6.*              |
|           | v1.6.4                | 1.26, 1.25, 1.24, 1.23        | 3.17.0         | 1.21.6        | 4.5.*              |
|           | v1.5.1                | 1.25, 1.24, 1.23              | 3.16.2         | 1.21.6        | 4.4.*              |
|           | v1.4.0                | 1.25, 1.24, 1.23, 1.22        | 3.16.2         | 1.19.10†      | 4.3.0              |
|           | v1.3.1                | 1.24, 1.23, 1.22, 1.21, 1.20  | 3.16.2         | 1.19.10†      | 4.2.5              |

See [Updating NGINX-Ingress to use the stable Ingress API (July 26, 2021)](https://kubernetes.io/blog/2021/07/26/update-with-ingress-nginx/)
to upgrade to the stable Ingress API before upgrading to Kubernetes 1.22.

## Get Involved

Thanks for taking the time to join our community and start contributing!

- This project adheres to the [Kubernetes Community Code of Conduct](https://git.k8s.io/community/code-of-conduct.md).
  By participating in this project, you agree to abide by its terms.
- **Contributing**: Documentation contributions are welcome.

  - Read [`CONTRIBUTING.md`](CONTRIBUTING.md) for information about the workflow that we
    expect and instructions on the developer certificate of origin that we require.
  - Join our Kubernetes Slack channel for developer discussion : [#ingress-nginx-dev](https://kubernetes.slack.com/archives/C021E147ZA4).
  - Submit GitHub issues for documentation problems.
    - Please make sure to read the [Issue Reporting Checklist](https://github.com/kubernetes/ingress-nginx/blob/main/CONTRIBUTING.md#issue-reporting-guidelines) before opening an issue. Issues not conforming to the guidelines **may be closed immediately**.

- **Support**:

  - Join the [#ingress-nginx-users](https://kubernetes.slack.com/messages/CANQGM8BA/) channel inside the [Kubernetes Slack](http://slack.kubernetes.io/) to ask questions or get support from the maintainers and other users.
  - The [GitHub issues](https://github.com/kubernetes/ingress-nginx/issues) in the repository are **exclusively** for bug reports and feature requests.

## License

[Apache License 2.0](https://github.com/kubernetes/ingress-nginx/blob/main/LICENSE)
