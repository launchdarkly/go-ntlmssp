# internal/md4

A verbatim copy of `golang.org/x/crypto/md4`, vendored from **v0.45.0**.

## Why this is vendored

NTLM computes its NT hash with MD4 (RFC 1320), so this package cannot be
swapped for a modern hash without breaking the protocol. MD4 is the *only*
thing this module ever used from `golang.org/x/crypto`.

Depending on the whole module for it was costly:

- Advisories for `golang.org/x/crypto` are recorded at **module** granularity,
  so Dependabot flagged this repository for every `x/crypto` CVE even though
  all of them were in `ssh`, `ssh/agent`, `ssh/knownhosts`, and `openpgp/*` --
  packages this module never imported. `govulncheck` correctly reported zero
  reachable vulnerabilities the entire time.
- Escaping those alerts by upgrading meant taking `x/crypto` v0.52.0, which
  declares `go 1.25` and would therefore have raised the minimum Go version
  for every consumer of this library.

Vendoring the one package we need takes this module to zero dependencies, so
neither problem can recur.

## Maintenance

Effectively none. MD4 is frozen (RFC 1320, published 1992), and the package is
deprecated upstream and will not change. `md4_test.go` and `example_test.go`
come from upstream and carry the published test vectors.

The upstream `Deprecated:` notice is kept deliberately: MD4 really is
cryptographically broken, and nothing outside this module's NTLM hashing should
use it.

## Local modifications

Only one, and it is outside the hashing code: `example_test.go` has its import
path rewritten from `golang.org/x/crypto/md4` to this package. `md4.go`,
`md4block.go`, `md4_test.go`, and `LICENSE` are byte-for-byte upstream, so
re-syncing is a plain copy plus that one-line import fix.

To keep them that way, the repository's `.golangci.yml` exempts this directory
from linting -- upstream trips `errcheck` on `hash.Write` calls, which never
return an error.

## Licensing

Upstream is BSD-3-Clause (`LICENSE` in this directory, copied from the
`golang.org/x/crypto` module root). The copyright headers on each file are
retained. Note this differs from the MIT license covering the rest of this
repository.
