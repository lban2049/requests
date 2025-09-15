# Changelog

This page provides a detailed log of all changes, improvements, and bug fixes for each version of the Requests library. For the most current, in-development changes, please see the `dev` section.

## dev (Unreleased)

### Deprecations
- Added support for Python 3.14.
- Dropped support for Python 3.8 following its end of support.

## 2.32.4 (2025-06-10)

### Security
- **CVE-2024-47081**: Fixed an issue where a maliciously crafted URL and trusted environment will retrieve credentials for the wrong hostname/machine from a netrc file.

### Improvements
- Numerous documentation improvements.

### Deprecations
- Added support for PyPy 3.11 for Linux and macOS.
- Dropped support for PyPy 3.9 following its end of support.

## 2.32.3 (2024-05-29)

### Bugfixes
- Fixed a bug that broke the ability to specify custom `SSLContexts` in sub-classes of `HTTPAdapter`. (#6716)
- Fixed an issue where Requests started failing to run on Python versions compiled without the `ssl` module. (#6724)

## 2.32.2 (2024-05-21)

### Deprecations
- To provide a more stable migration for custom `HTTPAdapters` impacted by the CVE changes in 2.32.0, we've renamed `_get_connection` to a new public API, `get_connection_with_tls_context`. Existing custom `HTTPAdapters` will need to migrate their code to use this new API. `get_connection` is considered deprecated in all versions of Requests >= 2.32.0.

## 2.32.1 (2024-05-20)

### Bugfixes
- Added missing test certs to the sdist distributed on PyPI.

## 2.32.0 (2024-05-20)

### Security
- Fixed an issue where setting `verify=False` on the first request from a Session would cause subsequent requests to the same origin to also ignore cert verification, regardless of the value of `verify`. ([GHSA-9wx4-h78v-vm56](https://github.com/psf/requests/security/advisories/GHSA-9wx4-h78v-vm56))

### Improvements
- `verify=True` now reuses a global `SSLContext` which should improve request time variance between first and subsequent requests. It should also minimize certificate load time on Windows systems when using a Python version built with OpenSSL 3.x. (#6667)
- Requests now supports optional use of character detection (`chardet` or `charset_normalizer`). The `Response.text()` and `apparent_encoding` APIs will default to `utf-8` if neither library is present. (#6702)

### Bugfixes
- Fixed a bug where emoji length was incorrectly calculated in the request content-length. (#6589)
- Fixed a deserialization bug in `JSONDecodeError`. (#6629)
- Fixed a bug where an extra leading `/` could lead `urllib3` to unnecessarily reparse the request URI. (#6644)

### Deprecations
- Added official support for CPython 3.12. (#6503)
- Added official support for PyPy 3.9 and 3.10. (#6641)
- Dropped official support for CPython 3.7. (#6642)
- Dropped official support for PyPy 3.7 and 3.8. (#6641)

### Packaging
- The project's source files are now located in `src/requests` in the sdist. (#6506)
- Starting in Requests 2.33.0, the build system will migrate to PEP 517 using `hatchling`.

## 2.31.0 (2023-05-22)

### Security
- **CVE-2023-32681**: Fixed a vulnerability where `Proxy-Authorization` headers could be forwarded to destination servers when following HTTPS redirects. Users who define proxy credentials in the URL are strongly encouraged to upgrade. See the full [GitHub Security Advisory](https://github.com/psf/requests/security/advisories/GHSA-j8r2-6x86-q33q) for details.

## 2.30.0 (2023-05-03)

### Dependencies
- Added support for `urllib3` 2.0. This may contain minor breaking changes. Users can pin to `urllib3<2` to stay on the 1.x series.

## 2.29.0 (2023-04-26)

### Improvements
- Deferred chunked requests to the `urllib3` implementation to improve standardization. (#6226)
- Relaxed header component requirements to support bytes/str subclasses. (#6356)

## 2.28.2 (2023-01-12)

### Dependencies
- Requests now supports `charset_normalizer` 3.x. (#6261)

### Bugfixes
- Updated `MissingSchema` exception to suggest `https` scheme rather than `http`. (#6188)

## 2.28.1 (2022-06-29)

### Improvements
- Optimized `iter_content` with a transition to `yield from`. (#6170)

### Dependencies
- Added support for `chardet` 5.0.0. (#6179)
- Added support for `charset-normalizer` 2.1.0. (#6169)

## 2.28.0 (2022-06-09)

### Deprecations
- **Dropped support for Python 2.7.**
- Dropped support for Python 3.6 (including pypy3.6).

### Improvements
- Wrapped JSON parsing issues in `requests.JSONDecodeError` for payloads without an encoding to make the `json()` API consistent. (#6097)
- Added provisional support for Python 3.11.

### Bugfixes
- Fixed bug where setting `CURL_CA_BUNDLE` to an empty string would disable cert verification. (#6074)
- Fixed an issue where invalid Windows registry entries caused proxy resolution to raise an exception. (#6149)

## 2.27.1 (2022-01-05)

### Bugfixes
- Fixed a parsing issue that resulted in the `auth` component being dropped from proxy URLs. (#6028)

... and so on for all previous versions.
