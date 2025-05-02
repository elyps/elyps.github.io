---
title: "How to build an Auto blog note 2"
meta_title: ""
description: "this is meta description"
date: 2022-04-04T05:00:00Z
image: "/images/image-placeholder.png"
categories: ["Application", "Data"]
author: "John Doe"
tags: ["nextjs", "blog"]
draft: false
---

<h1>{{ .Title }}</h1>
<p>{{ .Date.Format "02.01.2006" }}</p>

{{ if .Params.toc }}
  {{ partial "toc.html" . }}
{{ end }}

{{ range .Params.tags }}
  <a href="{{ "tags" | relURL }}/{{ . | urlize }}">{{ . }}</a>
{{ end }}