+++
title = "태그 목록"
type = "docs"
+++

## 태그 목록 - Tags

이 페이지는 사이트에서 사용되는 모든 태그들을 정리한 목록입니다.

<ul id="all-tags">
{{ range $name, $taxonomy := .Site.Taxonomies.tags }}
<li><a href="{{ "/tags/" | relLangURL }}{{ $name | urlize }}">{{ $name }}</a> ({{ $taxonomy.Count }})</li>
{{ end }}
</ul>
