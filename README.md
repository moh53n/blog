## Add a New Post

Create a new post from the blog archetype:

```powershell
hugo new blog/my-new-post.md
```

Edit the generated file in `content/blog/my-new-post.md`:

```yaml
---
title: "My New Post"
date: 2026-01-01T12:00:00+03:30
author: "Mohsen Tahmasebi"
description: "Short summary shown on the blog index and in metadata."
tags:
  - security
draft: true
slug: "my-new-post"
---
```

## Preview Drafts

Run the local server with drafts enabled:

```powershell
hugo server -D
```

## Publish a Post

When the post is ready, change:

```yaml
draft: true
```

to:

```yaml
draft: false
```

Build the site:

```powershell
hugo --minify
```

## Link to an External Post

For posts published somewhere else, add `external_url` to the front matter:

```yaml
external_url: "https://example.com/post"
```
