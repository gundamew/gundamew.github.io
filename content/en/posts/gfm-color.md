---
title: "Colored the Text on GitHub the Simple Way"
publishdate: "2019-08-21T12:05:00+08:00"
type: "post"
slug: "hello-world"
isCJKLanguage: false
---

I've found some colored text in this issue: [gohugoio/hugoThemes#682](https://github.com/gohugoio/hugoThemes/issues/682#issue-481516283)

> <span style="color:red;font-weight:bold;font-style:normal">- Broken themes will be removed around the end of September -</span>

But I remember GFM does not support inline styles. So I googled the topic and found the answer on [Stack Overflow](https://stackoverflow.com/a/39413824).

Looks like the author of this issue just use the syntax of Git diff to colored the text:

```diff
- text in red
+ text in green
! text in orange
# text in gray
```
