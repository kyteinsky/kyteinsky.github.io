+++
author = "Anupam Kumar"
title = "Embedding Asciinema in Hugo Websites"
date = "2021-06-10"
description = "Embedding asciinema casts in hugo websites"
tags = [
    "hugo",
    "asciinema",
    "cast",
    "stack",
    "theme",
]
categories = [
    "daily-progress",
]
series = ["Hugo", "Asciinema"]
aliases = ["embed-asciinema"]
asciinema = true
+++

&nbsp;

# Following a general approach for any theme, steps to be performed (following [this article](https://jenciso.github.io/blog/embedding-asciinema-cast-in-your-hugo-site/)):

Specific for the theme Stack: [Click Here](#for-the-stack-theme-the-kind-developer-of-the-theme-zhixuan666httpsgithubcomzhixuan666-mentioned-a-more-efficient-way-to-do-it)

* Get code for asciinema player
```sh
wget https://github.com/asciinema/asciinema-player/releases/download/v2.6.1/asciinema-player.css
wget https://github.com/asciinema/asciinema-player/releases/download/v2.6.1/asciinema-player.js
```

* Place them in static/css and static/js, where static is folder in the root dir of the website
* Copy the ```layouts/partials/_shared/head.html``` or ```layouts/partials/head/head.html``` from ```themes/<theme-name>``` directory to your website's root directory with the same folder structure
* Do the same with ```layouts/_default/baseof.html```
* Append this code block for css in ```layouts/partials/{head or _shared}/head.html```
```html
{{ if .Params.asciinema }}
    <link rel="stylesheet" type="text/css" href="{{ .Site.BaseURL }}css/asciinema-player.css" />
{{ end }}
```

* And this for js in ```layouts/_default/baseof.html```
```html
{{ if .Params.asciinema }}
    <script defer src="{{ .Site.BaseURL }}js/asciinema-player.js"></script>
{{ end }}
```
* Now let's make a shortcode for asciinema here: ```layouts/shortcodes/asciinema.html```

One can change the default rows and columns attributes inside ```<asciinema-player>``` tag
```html
<p>
    <asciinema-player
        src="/casts/{{ with .Get "key" }}{{ . }}{{ end }}"
        cols="{{ if .Get "cols" }}{{ .Get "cols" }}{{ else }}640{{ end }}"
        rows="{{ if .Get "rows" }}{{ .Get "rows" }}{{ else }}10{{ end }}"
        {{ if .Get "autoplay" }}autoplay="{{ .Get "autoplay" }}"{{ end }}
        {{ if .Get "preload" }}preload="{{ .Get "preload" }}"{{ end }}
        {{ if .Get "loop" }}loop="{{ .Get "loop" }}"{{ end }}
        start-at="{{ if .Get "start-at" }}{{ .Get "start-at" }}{{ else }}0{{ end }}"
        speed="{{ if .Get "speed" }}{{ .Get "speed" }}{{ else }}1{{ end }}"
        {{ if .Get "idle-time-limit" }}idle-time-limit="{{ .Get "idle-time-limit" }}"{{ end }}
        {{ if .Get "poster" }}poster="{{ .Get "poster" }}"{{ end }}
        {{ if .Get "font-size" }}font-size="{{ .Get "font-size" }}"{{ end }}
        {{ if .Get "theme" }}theme="{{ .Get "theme" }}"{{ end }}
        {{ if .Get "title" }}title="{{ .Get "title" }}"{{ end }}
        {{ if .Get "author" }}author="{{ .Get "author" }}"{{ end }}
        {{ if .Get "author-url" }}author-url="{{ .Get "author-url" }}"{{ end }}
        {{ if .Get "author-img-url" }}author-img-url="{{ .Get "author-img-url" }}"{{ end }}
    ></asciinema-player>
</p>
```

* Finally create a new post and set ```asciinema = true``` in the metadata (for toml)
* And embed the asciinema cast as follows in the body part
```html
{{</* asciinema key="demo-folder/demo-cast.cast" rows="35" preload="1" */>}}
```
* And don't forget to put your .cast file in ```static/casts/demo-folder/democast.cast```

---

## First attempt at the task

{{< asciinema key="embedding-asciinema-casts/embedding-asciinema-casts.cast" rows=35 preload="1" >}}

## Correcting the mistakes done above

{{< asciinema key="embedding-asciinema-casts/final.cast" rows=35 preload="1" >}}

&nbsp;

# For the Stack theme, the kind developer of the theme [@zhixuan666](https://github.com/zhixuan666) mentioned a more efficient way to do it.

Here is his reply to the GitHub issue: [link](https://github.com/CaiJimmy/hugo-theme-stack/issues/221#issuecomment-859537347)

&nbsp;
