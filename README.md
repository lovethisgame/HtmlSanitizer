HtmlSanitizer
=============

[![Version](https://img.shields.io/nuget/v/HtmlSanitizer.svg)](https://www.nuget.org/packages/HtmlSanitizer)

HtmlSanitizer is a .NET library for cleaning HTML fragments from constructs that can lead to [XSS attacks](https://en.wikipedia.org/wiki/Cross-site_scripting).
It uses the excellent C# jQuery port [CsQuery](https://github.com/jamietre/CsQuery) to parse, manipulate, and render HTML and CSS.

Because HtmlSanitizer is based on a robust HTML parser it can also shield you from deliberate or accidental
"tag poisoning" where invalid HTML in one fragment can corrupt the whole document leading to broken layout or style.

In order to facilitate different use cases, HtmlSanitizer can be customized at several levels:
   
- Configure allowed HTML tags through the property `AllowedTags`. All other tags will be stripped.
- Configure allowed HTML attributes through the property `AllowedAttributes`. All other attributes will be stripped.
- Configure allowed CSS property names through the property `AllowedCssProperties`. All other styles will be stripped.
- Configure allowed URI schemes through the property `AllowedSchemes`. All other URIs will be stripped.
- Configure HTML attributes that contain URIs (such as "src", "href" etc.) through the property `UriAttributes`.
- Provide a base URI that will be used to resolve relative URIs against.
- Cancelable events are raised before a tag, attribute, or style is removed.

### Tags allowed by default
`a, abbr, acronym, address, area, article, aside, b, bdi, big, blockquote, br, button, caption, center, cite, code, col, colgroup, data, datalist, dd, del, details, dfn, dir, div, dl, dt, em, fieldset, figcaption, figure, font, footer, form, h1, h2, h3, h4, h5, h6, header, hr, i, img, input, ins, kbd, keygen, label, legend, li, main, map, mark, menu, menuitem, meter, nav, ol, optgroup, option, output, p, pre, progress, q, rp, rt, ruby, s, samp, section, select, small, span, strike, strong, sub, summary, sup, table, tbody, td, textarea, tfoot, th, thead, time, tr, tt, u, ul, var, wbr`

### Attributes allowed by default
`abbr, accept, accept-charset, accesskey, action, align, alt, autocomplete, autosave, axis, bgcolor, border, cellpadding, cellspacing, challenge, char, charoff, charset, checked, cite, clear, color, cols, colspan, compact, contenteditable, coords, datetime, dir, disabled, draggable, dropzone, enctype, for, frame, headers, height, high, href, hreflang, hspace, ismap, keytype, label, lang, list, longdesc, low, max, maxlength, media, method, min, multiple, name, nohref, noshade, novalidate, nowrap, open, optimum, pattern, placeholder, prompt, pubdate, radiogroup, readonly, rel, required, rev, reversed, rows, rowspan, rules, scope, selected, shape, size, span, spellcheck, src, start, step, style, summary, tabindex, target, title, type, usemap, valign, value, vspace, width, wrap`

_Note:_ to prevent [classjacking](https://html5sec.org/#123) and interference with classes where the sanitized fragment is to be integrated, the `class` attribute is not in the whitelist by default. 
It can be added as follows:
```C#
var sanitizer = new HtmlSanitizer();
sanitizer.AllowedAttributes.Add("class");
var sanitized = sanitizer.Sanitize(html);
```

### CSS properties allowed by default
`background, background-attachment, background-color, background-image, background-position, background-repeat, border, border-bottom, border-bottom-color, border-bottom-style, border-bottom-width, border-collapse, border-color, border-left, border-left-color, border-left-style, border-left-width, border-right, border-right-color, border-right-style, border-right-width, border-spacing, border-style, border-top, border-top-color, border-top-style, border-top-width, border-width, bottom, caption-side, clear, clip, color, content, counter-increment, counter-reset, cursor, direction, display, empty-cells, float, font, font-family, font-size, font-style, font-variant, font-weight, height, left, letter-spacing, line-height, list-style, list-style-image, list-style-position, list-style-type, margin, margin-bottom, margin-left, margin-right, margin-top, max-height, max-width, min-height, min-width, opacity, orphans, outline, outline-color, outline-style, outline-width, overflow, padding, padding-bottom, padding-left, padding-right, padding-top, page-break-after, page-break-before, page-break-inside, quotes, right, table-layout, text-align, text-decoration, text-indent, text-transform, top, unicode-bidi, vertical-align, visibility, white-space, widows, width, word-spacing, z-index`

### URI schemes allowed by default
``http, https``

_Note:_ [Protocol-relative URLs](http://en.wikipedia.org/wiki/Wikipedia:Protocol-relative_URL)  (e.g. <a href="//github.com">//github.com</a>) are allowed by default (as are other relative URLs).

### Default attributes that contain URIs
`action, background, dynsrc, href, lowsrc, src`

Usage
-----

Install the [HtmlSanitizer NuGet package](https://www.nuget.org/packages/HtmlSanitizer/). Then:

```C#
var sanitizer = new HtmlSanitizer();
var html = @"<script>alert('xss')</script><div onload=""alert('xss')"""
    + @"style=""background-color: test"">Test<img src=""test.gif"""
    + @"style=""background-image: url(javascript:alert('xss')); margin: 10px""></div>";
var sanitized = sanitizer.Sanitize(html, "http://www.example.com");
Assert.That(sanitized, Is.EqualTo(@"<div style=""background-color: test"">"
    + @"Test<img style=""margin: 10px"" src=""http://www.example.com/test.gif""></div>");
```

License
-------

[MIT X11](http://en.wikipedia.org/wiki/MIT_License)
