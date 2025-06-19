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
transition: fade
---

# よくある実装

ブログ記事一覧の細粒度なAPIとデータフェッチ設計例

```tsx
export async function Page(props: {
  searchParams: Promise<{ page?: string }>;
}) {
  const searchParams = await props.searchParams;
  const page = searchParams.page ? Number(searchParams.page) : 1;
  const posts = await fetchPosts({
    page,
  }); // Post[]

  // ...`posts`を参照
}

type Post = {
  id: string;
  title: string;
  authorIds: string[];
  summary: string;
};
```

---

# よくある実装

ブログ記事一覧の細粒度なAPIとデータフェッチ設計例

```tsx {*}{maxHeight:'350px'}
export async function Page(props: {
  searchParams: Promise<{ page?: string }>;
}) {
  const searchParams = await props.searchParams;

  // ベースとなるブログ一覧を取得
  const page = searchParams.page ? Number(searchParams.page) : 1;
  const posts = await fetchPosts({
    page,
  });

  // ブログ一覧を補強する情報を一括で取得
  const postIds = posts.map((post) => post.id);
  const uniqueAuthorIds = Array.from(
    new Set(posts.flatMap((post) => post.authorIds)),
  );
  const [allAuthors, commentCounts, viewCounts] = await Promise.all([
    fetchAuthors(uniqueAuthorIds),
    fetchCommentCountsForPosts(postIds),
    fetchViewCountsForPosts(postIds),
  ]);

  // `richPosts: RichPost[]`を組み立て
  const authorsMap = new Map(allAuthors.map((author) => [author.id, author]));
  const commentCountsMap = new Map(
    commentCounts.map((item) => [item.postId, item.count]),
  );
  const viewCountsMap = new Map(
    viewCounts.map((item) => [item.postId, item.count]),
  );
  const richPosts = posts.map((post) => {
    const authors = post.authorIds
      .map((id) => authorsMap.get(id))
      .filter((author) => author !== undefined);
    const comments = commentCountsMap.get(post.id) ?? 0;
    const viewCount = viewCountsMap.get(post.id) ?? 0;

    return {
      ...post,
      authors,
      comments,
      viewCount,
    };
  });

  // ...`richPosts`を参照
}
```

---

# 問題点

やりたいことに対して、実装都合の概念が登場する

- やりたいことは「記事を取得する」「著者情報なども取得する」
- 実装都合で`RichPost[]`という概念が登場する
- もっと愚直にやりたいことを実装に表現したい

---

# 理想と考えられる実装

理想=Metaにおける考え方と、個人的な主観も入る

```tsx {*}{maxHeight:'350px'}
// page.tsx
export async function Page(props: {
  searchParams: Promise<{ page?: string }>;
}) {
  const searchParams = await props.searchParams;
  const page = searchParams.page ? Number(searchParams.page) : 1;
  const posts = await fetchPosts({
    page,
  });

  // `posts`をループして`<PostCassette>`を組み立てる
}

// post-cassette.tsx
export async function PostCassette({ post }: { post: Post }) {
  const [authors, comments, viewCount] = await Promise.all([
    fetchAuthors(post.authorIds),
    fetchCommentCount(post.id),
    fetchViewCount(post.id),
  ]);

  // ...`authors`, `comments`, `viewCount`を参照
}
```

---

# 問題点

このままだと致命的なパフォーマンス劣化が発生しうる

- 典型的なN+1の通信が発生する

---

# Batching/Caching

Metaで使われているアプローチ

- Batching: JavaScriptのマイクロタスクキューを「待つ」ことで、N+1を取りまとめる
- Caching: 同一データの取得はキャッシュされる

---

# DataLoader

RSC時代の設計をより「らしく」するライブラリ

- Batching/Cachingを容易に実現するライブラリ
- Meta（GraphQLのコミュニティ）より公開されている

```tsx
const authorLoader = new DataLoader(authorsBatch);
authorLoader.load("1");
authorLoader.load("2");
// 呼び出しはDataLoaderによってまとめられ、`authorsBatch(["1", "2"])`が呼び出される
```

---
layout: center
---

# DataLoader、ぜひ使ってみてください

---
layout: center
---

# End
