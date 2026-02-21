---
layout: post
title: "How to Build a Professional Portfolio with GitHub Pages"
date: 2026-02-21
author: Hugo Souza
tags: [Tutorials, GitHub Pages, Portfolio, Cloud]
reading_time: "5 min read"
---

# How to Build a Professional Portfolio with GitHub Pages

> ⏱️ **Estimated time:** 15 min | 🏷️ **Tags:** `#GitHub` `#Portfolio` `#Markdown`

## 📖 Summary

Creating a technical portfolio is one of the best ways to showcase your skills, share knowledge, and build a personal brand. In this guide, I'll show you step-by-step how to create a sleek, markdown-based portfolio using GitHub Pages—completely for free, without writing any complex HTML or CSS.

---

## 1. Introduction

Whether you're a Cloud Network Engineer, a Developer, or a DevOps professional, having a central hub for your documentation, procedures, and projects is crucial. Often, we scatter our notes across different platforms.

By leveraging **GitHub Pages** and standard Markdown (`.md`), you can create a repository that acts as both a version-controlled knowledge base and a public-facing website. It’s simple, free, and looks highly professional.

## 2. Engineering the Solution

We will use a standard GitHub repository. GitHub Pages natively supports Jekyll, a static site generator that transforms Markdown files into a beautiful website using a default theme (Primer).

### 2.1 Step 1: Create the Repository

1. Log in to your GitHub account.
2. Click on the **+** icon in the top right and select **New repository**.
3. Name your repository exactly like this: `<your-github-username>.github.io` (e.g., `hugothomazpsouza.github.io`).
4. Choose **Public** so the website can be accessed by anyone.
5. Check the box to **Add a README file**.
6. Click **Create repository**.

### 2.2 Step 2: Build the Structure

Clone the repository to your local machine and create the basic folder structure. A good knowledge base is well-organized.

```bash
# Clone your new repository
git clone git@github.com:<your-username>/<your-username>.github.io.git
cd <your-username>.github.io

# Create the recommended directories
mkdir -p docs/aws docs/gcp blog projects assets templates
```

### 2.3 Step 3: Write your main README.md

The `README.md` at the root of your repository will be the homepage of your website.

Edit your `README.md` to look something like this:

```markdown
<div align="center" markdown="1">
  <h1>☁️ Cloud & Network Engineering Portfolio</h1>
  <p><strong>Technical Knowledge Base, Projects, and Procedures</strong></p>
</div>

## 👨‍💻 About Me

I am a Cloud Network Engineer passionate about...

## 📚 Table of Contents

- [AWS Docs](docs/aws/README.md)
- [Blog](blog/README.md)
```

> **Important:** To ensure GitHub Pages correctly parses Markdown inside HTML tags, use `markdown="1"` inside your `<div>` tags if you choose to center text!

### 2.4 Step 4: Add Configuration to Fix Jekyll Build Issues

If you plan to use templates with `YYYY-MM-DD` placeholders, Jekyll might crash when trying to build your site because it expects strict dates.

To fix this, create a `_config.yml` file in the root of your repository and tell Jekyll to ignore the templates folder:

```yaml
theme: jekyll-theme-primer
exclude:
  - templates/
```

### 2.5 Step 5: Publish your First Article

Create an index for your blog in `blog/README.md` to list your articles, and then create your first post!

```bash
# Create the blog index
touch blog/README.md

# Create a new post
touch blog/2026-02-21-my-first-post.md
```

Write your content using standard markdown syntax. You can even include code snippets, tables, and images.

### 2.6 Step 6: Push to GitHub & Enable Pages

Commit and push your files:

```bash
git add .
git commit -m "feat: initial portfolio setup"
git push origin main
```

Now, configure GitHub Pages:

1. Go to your repository on GitHub.
2. Click **Settings** > **Pages**.
3. Under **Build and deployment**, set the Source to **Deploy from a branch**.
4. Select the `main` branch and `/ (root)` folder, then **Save**.

Wait 1-2 minutes, and check your new site at `https://<your-username>.github.io`!

## 3. Final Conclusion

Building a portfolio with GitHub Pages is straightforward and keeps you close to the tools you already use every day as an engineer—Git and Markdown. By organizing your learning and projects here, you create a lasting, shareable record of your technical journey.

---

## 💬 Leave a comment

Did this content help you set up your portfolio? **Leave a comment** on the project's issues or connect with me on [LinkedIn](https://www.linkedin.com/in/hugo-thomaz-pinheiro/) so we don't lose touch!
If the repository was useful for your studies, don't forget to click ⭐ at the top of the page.

[⬅️ Back to articles list](README.md)
