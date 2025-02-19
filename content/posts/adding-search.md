---
title: "Adding search to a Hugo static website using Pagefind"
date: 2024-09-03
draft: false
---

## Search search search ...
Search is on of those things that feels complex and infrastructure heavy, When I started thinking about how to add search to my personal website which is built with a static website generator.
I found a couple of Javascript libraries and mostly they would create indexes in the browser runtime, which wouldn't scale for a large number of documents and loads the client side with the indexing and the indexes like [Lunr](https://lunrjs.com/guides/getting_started.html),
then I stumbled upon the brilliant [PageFind](https://pagefind.app/docs/) which builds upon the basic ideas of static site generation and follows the architecture using `Github Actions`
It felt right immediately and I started integrating in my website so I added the following to my blog posts list page:


```go {linenos=true,hl_lines=[1-3],linenostart=1}
  {{ define "title" }} {{ .Title }} · {{ .Site.Title }} {{ end }}
  {{ define "content" }}
  {{- block "head" .}}
  <link href="/pagefind/pagefind-ui.css" rel="stylesheet" />
  <script src="/pagefind/pagefind-ui.js"></script>
  {{ end }}

  <section class="container list">
      <script>
          window.addEventListener("DOMContentLoaded", (event) => {
              new PagefindUI({ element: "#search", showSubResults: true });
          });
      </script>
      <h1 class="title">{{ .Title }}</h1>

      <ul>
          <li class="navigation-item">
              <div id="search"></div>
          </li>
          {{- range .Paginator.Pages -}} {{- .Render "li" -}} {{- end -}}
      </ul>

      {{ partial "pagination.html" . }}
  </section>
  {{ end }}
```
