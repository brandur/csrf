# Go stateless CSRF [![Build Status](https://github.com/brandur/csrf/workflows/csrf%20CI/badge.svg)](https://github.com/brandur/csrf/actions)

A stateless CSRF middleware for Go. It works by relying the
presence of the [`Origin`][origin] header. It will also
fall back to `Referer` if that's provided and `Origin`
isn't, but it has no mechanic for embedding a CSRF form
token.

Although [security best practices][csrf] still recommend
the use token-based strategy as primary defense, this isn't
because `Origin` isn't a secure way to verify an incoming
request, but more because in some corner cases browsers
won't pass an `Origin`, which may cause verification to
fail. This middleware aims to provide vastly simplified
CSRF protection, with the trade off that it will only work
99% of the time.

It also provides an easy way to provide working CSRF
protection across multiple services/domains that you own
without them having to engage in a complex cookies-sharing
scheme (a CSRF token is generally stored in a cookie). For
example, I use it to allow cross-origin requests to my
[newsletter signup][passages-signup] from my [main
site][sorg], but no others.

## Usage

`csrf.Protect` returns a function that will wrap another
`http.Handler`. For example:

``` go
import (
    "github.com/brandur/csrf"
)

func main() {
    // An `http.Handler` to wrap.
    var handler = http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {})

    // Wrap the `http.Handler` above and get a new `http.Handler`.
    handler = csrf.Protect(
        csrf.AllowedOrigin("https://example.com")
    )(handler)

    ...
}
```

[csrf]: https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)_Prevention_Cheat_Sheet
[origin]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Origin
[passages-signup]: https://github.com/brandur/passages-signup
[sorg]: https://github.com/brandur/sorg
