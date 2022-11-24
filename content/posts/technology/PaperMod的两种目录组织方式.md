---
title: "Hugo主题PaperMod目录结构的两种组织方式"
date: 2022-11-23T16:46:21+08:00
draft: false
weight: 2
cover: 
  image: "posts/technology/PaperMod的两种目录组织方式/冰山.jpg"
---

#### Hugo主题PaperMod目录结构的两种组织方式

hugo 的官方文档里定义了页面资源的组织方式，叫做[Page Bundles](https://gohugo.io/content-management/page-bundles/)。有Leaf Bundle和Branch Bundle两种。下面的文字引用自hugo官方文档：

## Leaf Bundles 

A *Leaf Bundle* is a directory at any hierarchy within the `content/` directory, that contains an **`index.md`** file.

### Examples of Leaf Bundle organization 

```text
content/
├── about
│   ├── index.md
├── posts
│   ├── my-post
│   │   ├── content1.md
│   │   ├── content2.md
│   │   ├── image1.jpg
│   │   ├── image2.png
│   │   └── index.md
│   └── my-other-post
│       └── index.md
│
└── another-section
    ├── ..
    └── not-a-leaf-bundle
        ├── ..
        └── another-leaf-bundle
            └── index.md
```

In the above example `content/` directory, there are four leaf bundles:

- `about`

  This leaf bundle is at the root level (directly under `content` directory) and has only the `index.md`.

- `my-post`

  This leaf bundle has the `index.md`, two other content Markdown files and two image files.

- image1, image2: These images are page resources of `my-post` and only available in `my-post/index.md` resources.
- content1, content2: These content files are page resources of `my-post` and only available in `my-post/index.md` resources. They will **not** be rendered as individual pages.

- `my-other-post`

  This leaf bundle has only the `index.md`.

- `another-leaf-bundle`

  This leaf bundle is nested under couple of directories. This bundle also has only the `index.md`.



The hierarchy depth at which a leaf bundle is created does not matter, as long as it is not inside another **leaf** bundle.

### Headless Bundle 

A headless bundle is a bundle that is configured to not get published anywhere:

- It will have no `Permalink` and no rendered HTML in `public/`.
- It will not be part of `.Site.RegularPages`, etc.

But you can get it by `.Site.GetPage`. Here is an example:

```go-html-template
{{ $headless := .Site.GetPage "/some-headless-bundle" }}
{{ $reusablePages := $headless.Resources.Match "author*" }}
<h2>Authors</h2>
{{ range $reusablePages }}
    <h3>{{ .Title }}</h3>
    {{ .Content }}
{{ end }}
```

*In this example, we are assuming the `some-headless-bundle` to be a headless bundle containing one or more **page** resources whose `.Name` matches `"author\*"`.*

Explanation of the above example:

1. Get the `some-headless-bundle` Page “object”.
2. Collect a *slice* of resources in this *Page Bundle* that matches `"author*"` using `.Resources.Match`.
3. Loop through that *slice* of nested pages, and output their `.Title` and `.Content`.

------

A leaf bundle can be made headless by adding below in the Front Matter (in the `index.md`):

```toml
headless = true
```

There are many use cases of such headless page bundles:

- Shared media galleries
- Reusable page content “snippets”

## Branch Bundles 

A *Branch Bundle* is any directory at any hierarchy within the `content/` directory, that contains at least an **`_index.md`** file.

This `_index.md` can also be directly under the `content/` directory.



Here `md` (markdown) is used just as an example. You can use any file type as a content resource as long as it is a content type recognized by Hugo.

### Examples of Branch Bundle organization 

```text
content/
├── branch-bundle-1
│   ├── branch-content1.md
│   ├── branch-content2.md
│   ├── image1.jpg
│   ├── image2.png
│   └── _index.md
└── branch-bundle-2
    ├── _index.md
    └── a-leaf-bundle
        └── index.md
```

In the above example `content/` directory, there are two branch bundles (and a leaf bundle):

- `branch-bundle-1`

  This branch bundle has the `_index.md`, two other content Markdown files and two image files.

- `branch-bundle-2`

  This branch bundle has the `_index.md` and a nested leaf bundle.



The hierarchy depth at which a branch bundle is created does not matter.

------

1. The `.md` extension is just an example. The extension can be `.html`, `.json` or any valid MIME type

我的这个博客项目目录结构同时使用了这两种方式：

* 在主页使用的是branch bundle
  * 在content/posts目录下使用的是branch bundle模式，posts目录和这五个入口文件件下都有一个_index.md文件。
  * 在这个五个入口文件夹下面，可以根据自己的习惯使用这两种不同的方式
    * branch bundle : 一个markdow-name.md文件，一个和该markdown文档同级新建一个同名称目录，在这个目录下可以保存page resource，也就是图片、音频、视频资源。在cover image里引用这些资源的格式为：posts/markdown-name/file-name.png
    * leaf bundle: 以要建立的markdown文档名称作为目录名，在这个目录下新建index.md文件和images文件夹。index.md是你要编辑的文档。images目录用来保存图片资源，当然images文件夹名称是自定义的，也可是img，根据自己的习惯。在文档中引用资源的格式为：images/filename.png。

