# kite-mcp-i18n

[![Go Reference](https://pkg.go.dev/badge/github.com/algo2go/kite-mcp-i18n.svg)](https://pkg.go.dev/github.com/algo2go/kite-mcp-i18n)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Locale-aware string lookups for the algo2go ecosystem. Embeds JSON
translation tables for English (`en`) and Hindi (`hi`); supports
`Accept-Language` header parsing, context-bound locale propagation,
and fallback to `en` for missing keys.

Used by [`Sundeepg98/kite-mcp-server`](https://github.com/Sundeepg98/kite-mcp-server)
for landing-page rendering, riskguard rejection messages, and OAuth
flow strings.

## Why a separate module?

Internationalization is an orthogonal cross-cutting primitive — usable
by any algo2go project (broker dashboards, payment flows, monitoring
UIs, future broker adapters) independent of `kite-mcp-server`. Hosting
it in its own module:

- Keeps translation contributions centralized (one repo to PR Hindi,
  Marathi, Tamil, etc.) instead of fragmenting per consumer
- Lets the `Locale` type and `Translate` API version independently of
  the server
- Reduces the dep-graph weight for consumers that only need locale
  resolution

## Stability promise

**v0.x — unstable.** Method signatures may break between minor versions.
Pin `v0.1.0` deliberately. v1.0 ships only after the public API
(`Locale`, `T`, `TFromContext`, `WithLocale`, `LocaleFromContext`,
`ParseAcceptLanguage`, `IsSupported`, `SupportedLocales`) is reviewed
for stability and at least one external consumer ships against it.

## Install

```bash
go get github.com/algo2go/kite-mcp-i18n@v0.1.0
```

## Public API (i18n.go)

- `type Locale string` — newtype for IETF BCP 47 language tags (`en`, `hi`, ...)
- `T(loc Locale, key string) string` — pure lookup, falls back to `en`
- `TFromContext(ctx, key) string` — context-aware lookup
- `WithLocale(ctx, loc) context.Context` / `LocaleFromContext(ctx) Locale`
- `ParseAcceptLanguage(header) Locale` — best-match from HTTP header
- `IsSupported(loc) bool`, `SupportedLocales() []Locale`
- `LocaleEN`, `LocaleHI` constants

## Translations

JSON files in `locales/`:
- `en.json` — English (canonical)
- `hi.json` — Hindi (Devanagari)

Keys are dot-namespaced (`error.action.home`, `landing.cta.signin`, ...).
PRs welcome for additional Indian-subcontinent locales (Marathi, Tamil,
Telugu, Bengali, etc.).

## Reference consumer

[`Sundeepg98/kite-mcp-server`](https://github.com/Sundeepg98/kite-mcp-server)
— used by:
- `app/http.go` for landing-page locale resolution + Accept-Language
- `kc/riskguard/middleware.go` for localized rejection messages
- OAuth + dashboard flows (via context propagation)

## License

MIT — see [LICENSE](LICENSE).

## Authors

Original design + en/hi translations: [Sundeepg98](https://github.com/Sundeepg98)
(Zerodha Tech). Multi-module promotion (2026-05-09): algo2go contributors.
