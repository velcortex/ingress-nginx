# Changelog

### controller-v1.16.0

First release of this community fork of [kubernetes/ingress-nginx](https://github.com/kubernetes/ingress-nginx),
based on `controller-v1.15.1` (the last upstream release before the project was retired).
The controller code, flags, annotations, IngressClass and Helm values are unchanged; this
release only updates the bundled components to clear known vulnerabilities.

Images:

* ghcr.io/velcortex/ingress-nginx/controller:v1.16.0@sha256:3f11293584b8b8c0ffc7dc2e419278645097a036414d0bad4e821a7679f1a679

### Security / component updates:

* NGINX: 1.27.1 -> 1.30.7 (NGINX keeps using PCRE, as before). Fixes the "NGINX2Root" set (CVE-2026-123007, CVE-2026-123633,
  CVE-2026-123634, CVE-2026-121810, CVE-2026-118899) and all earlier NGINX CVEs fixed
  between 1.27.2 and 1.30.7. The existing ingress-nginx/OpenResty patch set (patches 00-34)
  is re-based onto 1.30.7; no new patches are added.
* ModSecurity (libmodsecurity): 3.0.14 -> 3.0.17 (CVE-2026-42268, CVE-2026-30923,
  CVE-2026-52747, CVE-2026-73856, CVE-2026-73857, CVE-2026-104269). libmodsecurity now links PCRE2,
  which is required by ModSecurity >= 3.0.15.
* OpenResty components updated to the set that supports NGINX 1.30 (OpenResty 1.29.8.1):
  lua-nginx-module v0.10.31rc2, stream-lua-nginx-module v0.0.19rc2, lua-resty-core v0.1.34rc2,
  LuaJIT v2.1-20260415, ngx_devel_kit v0.3.4, headers-more v0.39, lua-upstream v0.08,
  lua-cjson 2.1.0.17, lua-resty-string v0.17, lua-resty-redis v0.33.
* Go: 1.26.1 -> 1.26.11; golang.org/x/net 0.63.0, golang.org/x/text 0.44.0, OpenTelemetry 1.47.0.
* OpenSSL: 3.5.9-r1 (Alpine security build).
* Base image: Alpine 3.23.3 -> 3.23.8; `patch` removed from the runtime image.

### Not applicable:

* CVE-2026-113425 (NGINX mail proxy): the mail module is not compiled in.

**Full Changelog**: https://github.com/velcortex/ingress-nginx/compare/controller-v1.15.1...controller-v1.16.0
