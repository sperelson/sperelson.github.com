---
layout: post
title: "Server Side Font Glyph Rendering"
description: "A demonstration of server-side font glyph rendering"
category: 
tags: [PHP, Font Glyph, Font Awesome, GD]
---
{% include JB/setup %}

Client side rendering of glyphs stored in font files is becoming quite prevalent. However, just because rendering the glyphs on the client side is the generally accepted method, doesn't mean all web browsers are capable.

In those times where the client web browser is lacking, typically for underpowered (and very cheap) feature phones, allowing the graceful fallback to a server hosted version of the glyph is preferable to displaying nothing.

### Technical Overview
I coded a quick and simple PHP class that renders a unicode character from the Font Awesome TTF file. You can visit the source at [<img src="http://hapnic.com/awesome2png/?u=F09B&h=18" alt="" />/sperelson/awesome2png](https://github.com/sperelson/awesome2png). And that Github icon was rendered from the server side.

