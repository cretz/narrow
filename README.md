# Narrow HTML

## Introduction

Narrow HTML (a.k.a. "NHTML" or just "Narrow") is a subset of HTML (a.k.a. "Narrow HTML" or "NHTML") and CSS (a.k.a.
"Narrow CSS" or "NCSS") designed to be easy to implement renderers for and be fast to serve.

This document serves as a specification. It is laid out as a series of bullet points for easy comprehension.

**Features/Goals**

* Compatible subset - all NHTML pages are HTML pages and render the same (i.e. no custom tags)
* Strict - strict validation and will fail on any error (onus placed on sit dev, not renderer implementor)
* Limited - fewer redundant convenience shortcuts and less ambiguity
* Easily parsable - excluding the doctype, parsable with XML parser
* No JS - no scriptability
* Terminal friendly - no complex interactivity

**WARNING: Work in progress**

This document is a work in progress. As a couple of applications/sites are built to serve NHTML, it will become easier
to understand the limitations of the spec and what needs to change.

## NHTML

1. Format
    1. All documents are UTF-8 encoded
    1. Mime type is always text/html
1. Doctype
    1. all documents start with `<!doctype html>`, followed by whitespace (max 10 chars), followed by
       `<html data-nhtml=""`
1. Tags
    1. All void tags must self-close, all non-void tags must have an explicit end tag
    1. All boolean attributes must have an empty value
    1. Attributes not explicitly mentioned are not allowed
    1. `id` attribute - Optional on all tags, must be unique in the document
1. `<html>` tag
    1. Required and only present as the first tag
    1. Must contain separate end tag
    1. Must contain exactly two children, the first is a `<head>` tag and the second is a `<body>` tag
    1. `data-nhtml` boolean attribute - required (presence of this attribute is what signifies it's an NHTML document)
1. `<head>` tag
    1. Required and only present as the first tag inside `<html>`
    1. `<meta charset="utf-8" />` is required and only present as the first child
    1. `<title>` tag is required and only present after all `<meta>` tags
    1. `<style>` and `<link>` child tags may be present after `<title>` in any order
1. `<meta>` void tag
    1. Must all be grouped as first set of child tags of `<head>` and only there
    1. `<meta charset="utf-8" />` is required and only present at the top of the group as the first child of `<head>`
    1. `charset` attribute - required on first `<meta>` tag and must be set to `utf8`. Disallowed anywhere else.
    1. `name` attribute
        1. Required on all but the first `<meta>` tag (disallowed there)
        1. Any content allowed, but unlike HTML for cases like `viewport` and `referrer`, the value is meaningless
    1. `content` attribute
        1. Required on all but the first `<meta>` tag (disallowed there)
1. `<title>` tag
    1. Required and single instance must be present directly after `<meta>` tags inside of `<head>`
1. `<link>` void tag
    1. Optional, anywhere after `<title>` tag inside of `<head>` and can have multiple
    1. Only used for stylesheets (for now)
    1. `href` attribute - required URL to stylesheet. Contents of URL must be valid NCSS.
    1. `rel` attribute - required and must be set to `stylesheet`
1. `<style>` tag
    1. Optional, anywhere after `<title>` tag inside of `<head>` and can have multiple
    1. Contents must be valid NCSS
1. `<body>` tag
    1. Required and only present as the second tag inside `<html>` after `<head>`
    1. Self and child tag attributes
        1. `class` attribute - optional, space-separated set of NCSS class names
        1. `style` attribute - optional, content must be valid NCSS
    1. All remaining "content" tags are only valid in a body
1. `<a>` content tag
    1. Content can't have nested `<a>`
    1. `href` attribute - required URL or URL fragment
1. `<br>` content void tag
1. `<em>` content tag
1. `<div>` content tag
1. `<h1>` - `<h6>` content tags
1. `<hr>` content void tag
1. `<img>` content void tag
    1. `alt` attribute - optional
    1. `src` attribute - required
1. `<li>` content tag
1. `<ol>` content tag
    1. Can only contain `<li>` tags
1. `<pre>` content tag
1. `<span>` content tag
1. `<strong>` content tag
1. `<table>` content tag
    1. May only contain `<thead>` tag (0 or 1), then `<tbody>` tags (1 or more), then `<tfoot>` tag (0 or 1)
1. `<tbody>` content tag
    1. Only available and at least one required in `<table>`, between `<thead>` and `<tfoot>` tags (if present)
    1. Can only contain `<tr>` tags
1. `<td>` content tag
    1. Only available as child of `<tr>`
1. `<tfoot>` content tag
    1. Only available as last tag inside `<table>`
    1. Can only contain `<tr>` tags
1. `<th>` content tag
    1. Only available as child of `<tr>`
1. `<thead>` content tag
    1. Only available as first tag inside `<table>`
    1. Can only contain `<tr>` tags
1. `<tr>` content tag
    1. Only available as child of `<tbody>`, `<tfoot>`, or `<thead>`
    1. Can only contain `<th>` and ``<td>` tags
1. `<ul>` content tag
    1. Can only contain `<li>` tags

TODO thoughts:

* Forms (of course...very important)
* `title` global attribute?
* Optional favicons
* Other `<link>` besides favicons such as iOS icons?
* Explain entities
* `<audio>` and `<video>`

## NCSS

TODO thoughts:

* I think I'd like to support only flexbox and css grid display options (and default display of block or inline, tbd)

## Future

TODO thoughts:

* Support partials (could hijack range requests) and/or DOM diffing to have some style of server-scripted+form
  interactivity? Makes this spec bleed into HTTP tho
* Confirm pandoc renderability
* Possible impls:
  * Canonical Rust w/ C FFI impl that generates reference img for other renderers to test against
    * Rust impl as WASM-able too? meh, AST-based so not that great (maybe the validator, but probably not worth it)
  * PDF or just some form that pandoc can accept
  * Terminal-based
  * Tor + WebRender + ...libui?
  * JS-to-canvas impl?