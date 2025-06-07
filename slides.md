---
theme: default
background: https://images.unsplash.com/photo-1504966981333-1ac8809be1ca?q=80&w=2670&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D
class: text-center
highlighter: shiki
lineNumbers: false
colorSchema: "dark"
drawings:
  persist: false
transition: slide-left
title: DataLoaderのすゝめ
mdc: true
---

# DataLoaderのすゝめ

React Server Componentsをより「らしく」する

---

# Profile

<div class="pb-5">
  <img src="https://avatars.githubusercontent.com/u/25711332?v=4" width="100" height="100">
</div>

```jsonc
// profile.jsonc
{
  "name": "佐藤 昭文",
  "alias": ["akfm_sato", "あっきー"],
  "job": "フロントエンドエキスパート",
  "tags": ["Next.js", "React", "Test", "リーン開発"],
  "sns": {
    "x": "akfm_sato",
    "zenn.dev": "akfm",
  },
}
```

---

# 要約

DataLoaderはいいぞ

- Metaはバッチングとキャッシュで、保守性とパフォーマンスを両立してる
- DataLoaderはこれを簡単に実現する手段
- Ryan Florence氏もbatch-loaderを公開したばかり
