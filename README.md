# hugo-hreflang

A [Hugo](https://gohugo.io/) module that renders `hreflang` alternates and an accessible language switcher for multilingual sites.

It handles the cases that usually go wrong:

- **Stable order.** Alternates follow the order of `hugo.Sites`, so the HTML does not change between builds when languages share a weight.
- **Pages with their own URL.** Translations linked with `translationKey` or by file name work whatever their `url` front matter.
- **Missing translations.** A page gets alternates only for the languages it is translated into. The switcher links the other languages to their home page, or leaves them out.
- **Pages that should not be indexed.** A page marked `noindex` gets no alternates and is not listed as an alternate of its translations.
- **No single-URL sets.** A page without indexable translations gets no `hreflang` tags at all.
- **`x-default`.** Points to the default language, to a language you choose, or nowhere.

## Requirements

Hugo 0.158.0 or later. The standard edition is enough.

## Installation

Import the module in your project configuration:

```toml
[module]
  [[module.imports]]
    path = "github.com/AmperisProducts/hugo-hreflang"
```

Your project must be a Hugo module (`hugo mod init github.com/you/your-site`), and Go must be installed to fetch the module. See [Use Hugo Modules](https://gohugo.io/hugo-modules/use-modules/).

## Usage

In the `head` element of your base template:

```go-html-template
{{ partial "hreflang/links.html" . }}
```

Output for a page translated into English and Spanish:

```html
<link rel="alternate" hreflang="en-US" href="https://example.org/products/tester/">
<link rel="alternate" hreflang="es-ES" href="https://example.org/es/productos/medidor-aislamiento/">
<link rel="alternate" hreflang="x-default" href="https://example.org/products/tester/">
```

Where you want the language switcher:

```go-html-template
{{ partial "hreflang/switcher.html" . }}
{{ partial "hreflang/switcher.html" (dict "page" . "class" "lang-menu" "label" "Choose a language") }}
```

The switcher is a `nav` with a list. The current language is marked with `aria-current` and is not a link, and every link has `lang` and `hreflang` attributes, so screen readers pronounce each language name correctly.

To build your own markup, use the data partial. It returns one entry per language with `name`, `label`, `hreflang`, `url`, `current`, `translated` and `indexable`:

```go-html-template
{{ range partial "hreflang/languages.html" . }}
  <a href="{{ .url }}" lang="{{ .hreflang }}">{{ .label }}</a>
{{ end }}
```

## Configuration

The defaults are:

```toml
[params.hreflang]
  useLocale = true          # hreflang="en-US" instead of "en"
  xDefault = "default"      # "default", a language key such as "en", or "" to omit it
  noindexParam = "noindex"  # front matter parameter that marks a page as not indexable
  switcherFallback = "home" # "home" or "none" for languages without a translation
```

With `useLocale`, the value comes from the `locale` setting of each language, and falls back to the language key when a language has no locale.

The switcher label comes from the `hreflangSwitcherLabel` translation key. The module includes it in English, Spanish, French, Portuguese, Italian and German.

The `exampleSite` directory contains an example in three languages. To build it: `cd exampleSite && hugo build`.

## License

[Apache License 2.0](LICENSE). Made by [Amperis Products S.L.](https://github.com/AmperisProducts), which uses this approach on its own multilingual Hugo site.
