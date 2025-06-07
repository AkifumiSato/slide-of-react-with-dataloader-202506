---
theme: default
background: https://images.unsplash.com/photo-1638414154639-0fbc5bceb80f?q=80&w=2670&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D
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

Meta流データフェッチ設計

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
transition: fade
---

# 先に結論

より詳細な内容を知りたい方は、Zennを読んでください

![](/public/zenn-capture.png)

---

# 先に結論

DataLoaderはいいぞ

- Metaはバッチングとキャッシュで、保守性とパフォーマンスを両立してる
- [DataLoader](https://www.npmjs.com/package/dataloader)はこれを簡単に実現する手段
- [batch-loader](https://www.npmjs.com/package/@ryanflorence/batch-loader)（Ryan Florence氏作）も同様の目的で作られたもの

---

#　MEMO

構成案メモ

- 見づらい実装を提示
- 見やすい実装を提示
- N+1問題
- バッチングとキャッシング
- DataLoader
