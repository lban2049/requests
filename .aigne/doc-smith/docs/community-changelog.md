# Changelog

This page provides a detailed log of all changes, improvements, and bug fixes for each version of the Requests library.

## dev

- [Short description of non-trivial change.]

### Deprecations
- Added support for Python 3.14.
- Dropped support for Python 3.8 following its end of support.

## 2.32.4 (2025-06-10)

### Security
- **CVE-2024-47081** Fixed an issue where a maliciously crafted URL and trusted environment will retrieve credentials for the wrong hostname/machine from a netrc file.

### Improvements
- Numerous documentation improvements.

### Deprecations
- Added support for pypy 3.11 for Linux and macOS.
- Dropped support for pypy 3.9 following its end of support.

## 2.32.3 (2024-05-29)

### Bugfixes
- Fixed bug breaking the ability to specify custom SSLContexts in sub-classes of HTTPAdapter. (#6716)
- Fixed issue where Requests started failing to run on Python versions compiled without the `ssl` module. (#6724)

## 2.32.2 (2024-05-21)

### Deprecations
- To provide a more stable migration for custom HTTPAdapters impacted by the CVE changes in 2.32.0, we've renamed `_get_connection` to a new public API, `get_connection_with_tls_context`. Existing custom HTTPAdapters will need to migrate their code to use this new API. `get_connection` is considered deprecated in all versions of Requests>=2.32.0.

  A minimal (2-line) example has been provided in the linked PR to ease migration, but we strongly urge users to evaluate if their custom adapter is subject to the same issue described in CVE-2024-35195. (#6710)

## 2.32.1 (2024-05-20)

### Bugfixes
- Add missing test certs to the sdist distributed on PyPI.

## 2.32.0 (2024-05-20)

### Security
- Fixed an issue where setting `verify=False` on the first request from a Session will cause subsequent requests to the _same origin_ to also ignore cert verification, regardless of the value of `verify`. ([GHSA-9wx4-h78v-vm56](https://github.com/psf/requests/security/advisories/GHSA-9wx4-h78v-vm56))

### Improvements
- `verify=True` now reuses a global SSLContext which should improve request time variance between first and subsequent requests. It should also minimize certificate load time on Windows systems when using a Python version built with OpenSSL 3.x. (#6667)
- Requests now supports optional use of character detection (`chardet` or `charset_normalizer`) when repackaged or vendored. This enables `pip` and other projects to minimize their vendoring surface area. The `Response.text()` and `apparent_encoding` APIs will default to `utf-8` if neither library is present. (#6702)

### Bugfixes
- Fixed bug in length detection where emoji length was incorrectly calculated in the request content-length. (#6589)
- Fixed deserialization bug in JSONDecodeError. (#6629)
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
- Versions of Requests between v2.3.0 and v2.30.0 are vulnerable to potential forwarding of `Proxy-Authorization` headers to destination servers when following HTTPS redirects. Full details can be read in our [Github Security Advisory](https://github.com/psf/requests/security/advisories/GHSA-j8r2-6x86-q33q) and [CVE-2023-32681](https://nvd.nist.gov/vuln/detail/CVE-2023-32681).

## 2.30.0 (2023-05-03)

### Dependencies
- ⚠️ Added support for urllib3 2.0. ⚠️ This may contain minor breaking changes. We advise reviewing the [urllib3 v2 Migration Guide](https://urllib3.readthedocs.io/en/latest/v2-migration-guide.html). Users who wish to stay on urllib3 1.x can pin to `urllib3<2`.

## 2.29.0 (2023-04-26)

### Improvements
- Requests now defers chunked requests to the urllib3 implementation to improve standardization. (#6226)
- Requests relaxes header component requirements to support bytes/str subclasses. (#6356)

## 2.28.2 (2023-01-12)

### Dependencies
- Requests now supports charset_normalizer 3.x. (#6261)

### Bugfixes
- Updated MissingSchema exception to suggest https scheme rather than http. (#6188)

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
- Wrap JSON parsing issues in Request's JSONDecodeError for payloads without an encoding to make `json()` API consistent. (#6097)
- Parse header components consistently, raising an InvalidHeader error in all invalid cases. (#6154)
- Added provisional 3.11 support with current beta build. (#6155)
- Requests got a makeover and we decided to paint it black. (#6095)

### Bugfixes
- Fixed bug where setting `CURL_CA_BUNDLE` to an empty string would disable cert verification. (#6074)
- Fixed urllib3 exception leak, wrapping `urllib3.exceptions.SSLError` with `requests.exceptions.SSLError` for `content` and `iter_content`. (#6057)
- Fixed issue where invalid Windows registry entries caused proxy resolution to raise an exception. (#6149)
- Fixed issue where entire payload could be included in the error message for JSONDecodeError. (#6036)

## 2.27.1 (2022-01-05)

### Bugfixes
- Fixed parsing issue that resulted in the `auth` component being dropped from proxy URLs. (#6028)

## 2.27.0 (2022-01-03)

### Improvements
- Officially added support for Python 3.10. (#5928)
- Added a `requests.exceptions.JSONDecodeError` to unify JSON exceptions. (#5856)
- Improved error text for misnamed `InvalidSchema` and `MissingSchema` exceptions. (#6017)
- Improved proxy parsing for proxy URLs missing a scheme. (#5917)

### Bugfixes
- Fixed defect in `extract_zipped_paths` which could result in an infinite loop. (#5851)
- Fixed handling for `AttributeError` when calculating length of files from `Tarfile.extractfile()`. (#5239)
- Fixed urllib3 exception leak, wrapping `urllib3.exceptions.InvalidHeader`. (#5914)
- Fixed bug where two Host headers were sent for chunked requests. (#5391)
- Fixed regression where `Proxy-Authorization` was incorrectly stripped from requests. (#5924)
- Fixed performance regression for hosts with a large number of proxies. (#5924)
- Fixed idna exception leak, wrapping `UnicodeError` with `requests.exceptions.InvalidURL`. (#5414)

### Deprecations
- Requests support for Python 2.7 and 3.6 will be ending in 2022. Requests 2.27.x is likely to be the last release series providing support.

## 2.26.0 (2021-07-13)

### Improvements
- Requests now supports Brotli compression, if either `brotli` or `brotlicffi` is installed. (#5783)
- `Session.send` now correctly resolves proxy configurations. (#5681)

### Bugfixes
- Fixed a race condition in zip extraction when using Requests in parallel. (#5707)

### Dependencies
- Instead of `chardet`, use `charset_normalizer` for Python 3. `chardet` will be used if already installed for backward compatibility. (#5797)
- Requests now supports `idna` 3.x on Python 3. (#5711)

### Deprecations
- The `requests[security]` extra has been converted to a no-op install. PyOpenSSL is no longer recommended. (#5867)
- Requests has officially dropped support for Python 3.5. (#5867)

## 2.25.1 (2020-12-16)

### Bugfixes
- Requests now treats `application/json` as `utf8` by default. (#5673)

### Dependencies
- Requests now supports chardet v4.x.

## 2.25.0 (2020-11-11)

### Improvements
- Added support for NETRC environment variable. (#5643)

### Dependencies
- Requests now supports urllib3 v1.26.

### Deprecations
- Requests v2.25.x will be the last release series with support for Python 3.5.
- The `requests[security]` extra is officially deprecated and will be removed in Requests v2.26.0.

## 2.24.0 (2020-06-17)

### Improvements
- pyOpenSSL TLS implementation is now only used if Python lacks an `ssl` module or SNI support. (#5443)
- Redirect resolution should now only occur when `allow_redirects` is True. (#5492)
- No longer perform unnecessary Content-Length calculation. (#5496)

## 2.23.0 (2020-02-19)

### Improvements
- Remove defunct reference to `prefetch` in Session `__attrs__`. (#5110)

### Bugfixes
- Requests no longer outputs password in basic auth usage warning. (#5099)

### Dependencies
- Pinning for `chardet` and `idna` now uses major version instead of minor.

## 2.22.0 (2019-05-15)

### Dependencies
- Requests now supports urllib3 v1.25.2. (note: 1.25.0 and 1.25.1 are incompatible)

### Deprecations
- Requests has officially stopped support for Python 3.4.

## 2.21.0 (2018-12-10)

### Dependencies
- Requests now supports idna v2.8.

## 2.20.1 (2018-11-08)

### Bugfixes
- Fixed bug with unintended Authorization header stripping for redirects using default ports.

## 2.20.0 (2018-10-18)

### Bugfixes
- Content-Type header parsing is now case-insensitive.
- Fixed exception leak where certain redirect urls would raise uncaught urllib3 exceptions.
- Requests removes Authorization header from requests redirected from https to http on the same hostname. (CVE-2018-18074)
- `should_bypass_proxies` now handles URIs without hostnames.

### Dependencies
- Requests now supports urllib3 v1.24.

### Deprecations
- Requests has officially stopped support for Python 2.6.

## 2.19.1 (2018-06-14)

### Bugfixes
- Fixed issue where status_codes.py's `init` function failed.

## 2.19.0 (2018-06-12)

### Improvements
- Warn user about possible slowdown when using cryptography version < 1.3.4.
- Check for invalid host in proxy URL.
- Fragments are now properly maintained across redirects.
- Added support for SHA-256 and SHA-512 digest auth algorithms.

### Bugfixes
- Parsing empty `Link` headers no longer returns a bogus entry.
- Fixed `IOError` when loading default certificate bundle from a zip archive.
- Properly normalize adapter prefixes for url comparison.

## 2.18.4 (2017-08-15)

### Improvements
- Error messages for invalid headers now include the header name.

### Dependencies
- We now support idna v2.6.

## 2.13.0 (2017-01-24)

### Features
- Only load the `idna` library when we've determined we need it.

### Miscellaneous
- Updated bundled urllib3 to 1.20.
- Updated bundled idna to 2.2.

## 2.12.0 (2016-11-15)

### Improvements
- Updated support for internationalized domain names from IDNA2003 to IDNA2008.
- Improved heuristics for guessing content lengths.
- Requests now tolerates empty passwords in proxy credentials.

### Bugfixes
- When calling `response.close`, the call is propagated to non-urllib3 backends.
- Fixed issue where `ALL_PROXY` would be preferred over scheme-specific variables.

## 2.11.0 (2016-08-08)

### Improvements
- Added support for the `ALL_PROXY` environment variable.
- Reject header values that contain leading whitespace or newline characters.

### Bugfixes
- Fixed occasional `TypeError` when decoding a JSON response in an error case.
- Fixed a bug when sending JSON data that could cause obscure OpenSSL errors.

## 2.10.0 (2016-04-29)

### New Features
- SOCKS Proxy Support! (requires PySocks)

## 2.9.1 (2015-12-21)

### Bugfixes
- Resolve regression making it impossible to send binary strings as bodies in Python 3.
- Fixed errors when calculating cookie expiration dates in certain locales.

## 2.9.0 (2015-12-15)

### Minor Improvements
- The `verify` keyword argument now supports a path to a directory of CA certificates.
- Warnings are emitted when sending files opened in text mode.

### Bugfixes
- We now send the content length for the number of bytes we will actually read, allowing partial file uploads.
- Sessions are now closed in all cases when using the functional API.

## 2.8.0 (2015-10-05)

### Minor Improvements
- Requests now supports per-host proxies.
- `Response.raise_for_status` now prints the URL that failed.

### Bugfixes
- The `json` parameter will only be used if neither `data` nor `files` are present.
- We now ignore empty fields in the `NO_PROXY` environment variable.

## 2.7.0 (2015-05-03)

### Bugfixes
- Updated urllib3 to 1.10.4, resolving several bugs involving chunked transfer encoding.

## 2.6.0 (2015-03-14)

### Bugfixes
- **CVE-2015-2296**: Fix handling of cookies on redirect to prevent session fixation attacks.

### Features and Improvements
- Support bytearrays in the `files` argument.

## 2.5.2 (2015-02-23)

### Features and Improvements
- Add sha256 fingerprint support.

### Security
- Pulled in an updated `cacert.pem`.
- Drop RC4 from the default cipher list.

## 2.4.2 (2014-10-05)

### Improvements
- Added `json` parameter for uploads.
- Support for bytestring URLs on Python 3.x.

## 2.4.0 (2014-08-29)

### Behavioral Changes
- `Connection: keep-alive` header is now sent automatically.

### Improvements
- Support for connect timeouts! Timeout now accepts a tuple (connect, read).

## 2.3.0 (2014-05-16)

### API Changes
- New `Response` property `is_redirect`.
- The `timeout` parameter now affects requests with `stream=True` and `stream=False` equally.

### Bugfixes
- No longer expose Authorization or Proxy-Authorization headers on redirect. (CVE-2014-1829, CVE-2014-1830).

## 2.0.0 (2013-09-24)

### API Changes
- Keys in the Headers dictionary are now native strings on all Python versions.
- Proxy URLs now *must* have an explicit scheme.
- `RequestException` is now a subclass of `IOError`.

### Bugfixes
- Vastly improved proxy support, including the CONNECT verb.
- Cookies are now properly managed when 401 authentication responses are received.

## 1.2.0 (2013-03-31)

- Major proxy work including parsing of proxy authentication from the proxy url.
- Add `elapsed` attribute to `Response` objects to time how long a request took.

## 1.1.0 (2013-01-10)

- CHUNKED REQUESTS.
- Support for iterable response bodies.

## 1.0.0 (2012-12-17)

- Massive Refactor and Simplification.
- Switch to Apache 2.0 license.
- Swappable and Mountable Connection Adapters.
- Removal of all hooks except 'response'.

## 0.14.0 (2012-09-02)

- No more iter_content errors if already downloaded.

## 0.13.4 (2012-07-27)

- GSSAPI/Kerberos authentication!

## 0.12.0 (2012-05-02)

- EXPERIMENTAL OAUTH SUPPORT!
- Proper CookieJar-backed cookies interface.

## 0.10.1 (2012-01-23)

- PYTHON 3 SUPPORT!
- Dropped 2.5 Support. (*Backwards Incompatible*)

## 0.10.0 (2012-01-21)

- `Response.content` is now bytes-only. (*Backwards Incompatible*)
- New `Response.text` is unicode-only.

## 0.8.8 (2011-12-28)

- SSL CERT VERIFICATION!
- New 'verify' argument for SSL requests.

## 0.8.0 (2011-11-13)

- Keep-alive support!
- Complete removal of Urllib2.

## 0.6.3 (2011-10-13)

- Beautiful `requests.async` module, for making async requests w/ gevent.

## 0.6.0 (2011-08-17)

- New persistent sessions object and context manager.
- Transparent Dict-cookie handling.

## 0.5.1 (2011-07-23)

- International Domain Name Support!

## 0.5.0 (2011-06-21)

- PATCH Support.
- Support for Proxies.

## 0.2.0 (2011-02-14)

- Birth!

## 0.0.1 (2011-02-13)

- Frustration.
- Conception.
