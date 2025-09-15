# Changelog

This page provides a detailed history of the Requests library, documenting all notable changes, improvements, bug fixes, and deprecations for each release. It serves as a comprehensive log for developers to track the evolution of the project.

## Unreleased

- [Short description of non-trivial change.]

### Deprecations

- Added support for Python 3.14.
- Dropped support for Python 3.8 following its end of support.

## 2.32.4 (2025-06-10)

### Security

- **CVE-2024-47081**: Fixed an issue where a maliciously crafted URL and trusted environment will retrieve credentials for the wrong hostname/machine from a netrc file.

### Improvements

- Numerous documentation improvements

### Deprecations

- Added support for pypy 3.11 for Linux and macOS.
- Dropped support for pypy 3.9 following its end of support.

## 2.32.3 (2024-05-29)

### Bugfixes

- Fixed bug breaking the ability to specify custom SSLContexts in sub-classes of `HTTPAdapter`. (#6716)
- Fixed issue where Requests started failing to run on Python versions compiled without the `ssl` module. (#6724)

## 2.32.2 (2024-05-21)

### Deprecations

- To provide a more stable migration for custom `HTTPAdapters` impacted by the CVE changes in 2.32.0, we've renamed `_get_connection` to a new public API, `get_connection_with_tls_context`. Existing custom `HTTPAdapters` will need to migrate their code to use this new API. `get_connection` is considered deprecated in all versions of Requests >= 2.32.0.

  A minimal (2-line) example has been provided in the linked PR to ease migration, but we strongly urge users to evaluate if their custom adapter is subject to the same issue described in CVE-2024-35195. (#6710)

## 2.32.1 (2024-05-20)

### Bugfixes

- Add missing test certs to the sdist distributed on PyPI.

## 2.32.0 (2024-05-20)

### Security

- Fixed an issue where setting `verify=False` on the first request from a `Session` will cause subsequent requests to the *same origin* to also ignore cert verification, regardless of the value of `verify`. (GHSA-9wx4-h78v-vm56)

### Improvements

- `verify=True` now reuses a global SSLContext which should improve request time variance between first and subsequent requests. It should also minimize certificate load time on Windows systems when using a Python version built with OpenSSL 3.x. (#6667)
- Requests now supports optional use of character detection (`chardet` or `charset_normalizer`) when repackaged or vendored. This enables `pip` and other projects to minimize their vendoring surface area. The `Response.text()` and `apparent_encoding` APIs will default to `utf-8` if neither library is present. (#6702)

### Bugfixes

- Fixed bug in length detection where emoji length was incorrectly calculated in the request content-length. (#6589)
- Fixed deserialization bug in `JSONDecodeError`. (#6629)
- Fixed bug where an extra leading `/` (path separator) could lead urllib3 to unnecessarily reparse the request URI. (#6644)

### Deprecations

- Requests has officially added support for CPython 3.12 (#6503)
- Requests has officially added support for PyPy 3.9 and 3.10 (#6641)
- Requests has officially dropped support for CPython 3.7 (#6642)
- Requests has officially dropped support for PyPy 3.7 and 3.8 (#6641)

### Documentation

- Various typo fixes and doc improvements.

### Packaging

- Requests has started adopting some modern packaging practices. The source files for the projects (formerly `requests`) is now located in `src/requests` in the Requests sdist. (#6506)
- Starting in Requests 2.33.0, Requests will migrate to a PEP 517 build system using `hatchling`. This should not impact the average user, but extremely old versions of packaging utilities may have issues with the new packaging format.

## 2.31.0 (2023-05-22)

### Security

- **CVE-2023-32681**: Versions of Requests between v2.3.0 and v2.30.0 are vulnerable to potential forwarding of `Proxy-Authorization` headers to destination servers when following HTTPS redirects.

  When proxies are defined with user info (`https://user:pass@proxy:8080`), Requests will construct a `Proxy-Authorization` header that is attached to the request to authenticate with the proxy.

  In cases where Requests receives a redirect response, it previously reattached the `Proxy-Authorization` header incorrectly, resulting in the value being sent through the tunneled connection to the destination server. Users who rely on defining their proxy credentials in the URL are *strongly* encouraged to upgrade to Requests 2.31.0+ to prevent unintentional leakage and rotate their proxy credentials once the change has been fully deployed.

  Users who do not use a proxy or do not supply their proxy credentials through the user information portion of their proxy URL are not subject to this vulnerability.

  Full details can be read in our [Github Security Advisory](https://github.com/psf/requests/security/advisories/GHSA-j8r2-6x86-q33q).

## 2.30.0 (2023-05-03)

### Dependencies

- ⚠️ Added support for urllib3 2.0. ⚠️

  This may contain minor breaking changes so we advise careful testing and reviewing the [urllib3 v2 Migration Guide](https://urllib3.readthedocs.io/en/latest/v2-migration-guide.html) prior to upgrading.

  Users who wish to stay on urllib3 1.x can pin to `urllib3<2`.

## 2.29.0 (2023-04-26)

### Improvements

- Requests now defers chunked requests to the urllib3 implementation to improve standardization. (#6226)
- Requests relaxes header component requirements to support bytes/str subclasses. (#6356)

## 2.28.2 (2023-01-12)

### Dependencies

- Requests now supports `charset_normalizer` 3.x. (#6261)

### Bugfixes

- Updated `MissingSchema` exception to suggest `https` scheme rather than `http`. (#6188)

## 2.28.1 (2022-06-29)

### Improvements

- Speed optimization in `iter_content` with transition to `yield from`. (#6170)

### Dependencies

- Added support for chardet 5.0.0 (#6179)
- Added support for charset-normalizer 2.1.0 (#6169)

## 2.28.0 (2022-06-09)

### Deprecations

- ⚠️ Requests has officially dropped support for Python 2.7. ⚠️ (#6091)
- Requests has officially dropped support for Python 3.6 (including pypy3.6). (#6091)

### Improvements

- Wrap JSON parsing issues in Request's `JSONDecodeError` for payloads without an encoding to make `json()` API consistent. (#6097)
- Parse header components consistently, raising an `InvalidHeader` error in all invalid cases. (#6154)
- Added provisional 3.11 support with current beta build. (#6155)
- Requests got a makeover and we decided to paint it black. (#6095)

### Bugfixes

- Fixed bug where setting `CURL_CA_BUNDLE` to an empty string would disable cert verification. All Requests 2.x versions before 2.28.0 are affected. (#6074)
- Fixed urllib3 exception leak, wrapping `urllib3.exceptions.SSLError` with `requests.exceptions.SSLError` for `content` and `iter_content`. (#6057)
- Fixed issue where invalid Windows registry entries caused proxy resolution to raise an exception rather than ignoring the entry. (#6149)
- Fixed issue where entire payload could be included in the error message for `JSONDecodeError`. (#6036)

## 2.27.1 (2022-01-05)

### Bugfixes

- Fixed parsing issue that resulted in the `auth` component being dropped from proxy URLs. (#6028)

## 2.27.0 (2022-01-03)

### Improvements

- Officially added support for Python 3.10. (#5928)
- Added a `requests.exceptions.JSONDecodeError` to unify JSON exceptions between Python 2 and 3. This gets raised in the `response.json()` method, and is backwards compatible as it inherits from previously thrown exceptions. Can be caught from `requests.exceptions.RequestException` as well. (#5856)
- Improved error text for misnamed `InvalidSchema` and `MissingSchema` exceptions. This is a temporary fix until exceptions can be renamed (Schema->Scheme). (#6017)
- Improved proxy parsing for proxy URLs missing a scheme. This will address recent changes to `urlparse` in Python 3.9+. (#5917)

### Bugfixes

- Fixed defect in `extract_zipped_paths` which could result in an infinite loop for some paths. (#5851)
- Fixed handling for `AttributeError` when calculating length of files obtained by `Tarfile.extractfile()`. (#5239)
- Fixed urllib3 exception leak, wrapping `urllib3.exceptions.InvalidHeader` with `requests.exceptions.InvalidHeader`. (#5914)
- Fixed bug where two Host headers were sent for chunked requests. (#5391)
- Fixed regression in Requests 2.26.0 where `Proxy-Authorization` was incorrectly stripped from all requests sent with `Session.send`. (#5924)
- Fixed performance regression in 2.26.0 for hosts with a large number of proxies available in the environment. (#5924)
- Fixed idna exception leak, wrapping `UnicodeError` with `requests.exceptions.InvalidURL` for URLs with a leading dot (.) in the domain. (#5414)

### Deprecations

- Requests support for Python 2.7 and 3.6 will be ending in 2022. While we don't have exact dates, Requests 2.27.x is likely to be the last release series providing support.

## 2.26.0 (2021-07-13)

### Improvements

- Requests now supports Brotli compression, if either the `brotli` or `brotlicffi` package is installed. (#5783)
- `Session.send` now correctly resolves proxy configurations from both the Session and Request. Behavior now matches `Session.request`. (#5681)

### Bugfixes

- Fixed a race condition in zip extraction when using Requests in parallel from zip archive. (#5707)

### Dependencies

- Instead of `chardet`, use the MIT-licensed `charset_normalizer` for Python3 to remove license ambiguity for projects bundling requests. If `chardet` is already installed on your machine it will be used instead of `charset_normalizer` to keep backwards compatibility. (#5797)

You can also install `chardet` while installing requests by specifying `[use_chardet_on_py3]` extra as follows:

```shell
pip install "requests[use_chardet_on_py3]"
```

Python 2 still depends upon the `chardet` module.

- Requests now supports `idna` 3.x on Python 3. `idna` 2.x will continue to be used on Python 2 installations. (#5711)

### Deprecations

- The `requests[security]` extra has been converted to a no-op install. PyOpenSSL is no longer the recommended secure option for Requests. (#5867)
- Requests has officially dropped support for Python 3.5. (#5867)

## 2.25.1 (2020-12-16)

### Bugfixes

- Requests now treats `application/json` as `utf8` by default. Resolving inconsistencies between `r.text` and `r.json` output. (#5673)

### Dependencies

- Requests now supports chardet v4.x.

## 2.25.0 (2020-11-11)

### Improvements

- Added support for `NETRC` environment variable. (#5643)

### Dependencies

- Requests now supports urllib3 v1.26.

### Deprecations

- Requests v2.25.x will be the last release series with support for Python 3.5.
- The `requests[security]` extra is officially deprecated and will be removed in Requests v2.26.0.

... and so on for all previous versions.