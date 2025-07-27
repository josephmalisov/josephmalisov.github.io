---
layout: post
title: "Getting Started with Jekyll for GitHub Pages"
date: 9999-12-31 10:30:00 -0800
categories: tutorial jekyll github-pages
---

## Setting Up Your First Jekyll Blog

I recently set up this blog using Jekyll and GitHub Pages, and I wanted to share the process with you. Jekyll is a fantastic static site generator that's perfect for blogs, and when combined with GitHub Pages, you get free hosting with automatic deployments.

### Why Jekyll + GitHub Pages?

- **Free hosting** - GitHub Pages provides free hosting for static sites
- **Version control** - Your entire blog is version controlled with Git
- **Custom domain support** - Easy to set up a custom domain
- **Automatic builds** - Every push to main triggers a new build
- **Great community** - Tons of themes and plugins available

### Prerequisites

Before we start, make sure you have:
- Ruby installed (version 2.4.0 or higher)
- Git installed
- A GitHub account

### Step 1: Install Jekyll

First, install Jekyll and Bundler:

```bash
gem install jekyll bundler
```

### Step 2: Create a New Jekyll Site

Create a new Jekyll site in your repository:

```bash
jekyll new your-blog-name
cd your-blog-name
```

### Step 3: Configure Your Site

Edit the `_config.yml` file to customize your site:

```yaml
title: Your Blog Title
email: your-email@example.com
description: Your blog description
baseurl: ""
url: "https://yourusername.github.io"
twitter_username: yourusername
github_username: yourusername
```

### Step 4: Test Locally

Run the development server to test your site:

```bash
bundle exec jekyll serve
```

Visit `http://localhost:4000` to see your site.

### Step 5: Deploy to GitHub Pages

1. Create a new repository on GitHub named `yourusername.github.io`
2. Push your Jekyll site to the repository:

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/yourusername/yourusername.github.io.git
git push -u origin main
```

### Step 6: Enable GitHub Pages

1. Go to your repository settings on GitHub
2. Scroll down to the "Pages" section
3. Select "Deploy from a branch"
4. Choose the "main" branch and "/ (root)" folder
5. Click "Save"

Your site will be available at `https://yourusername.github.io` in a few minutes!

### Customization Tips

- **Themes**: Jekyll comes with several built-in themes, or you can use custom ones
- **Posts**: Add new posts in the `_posts` directory with the format `YYYY-MM-DD-title.md`
- **Pages**: Create new pages in the root directory
- **Layouts**: Customize the look and feel by editing the theme files

### Next Steps

Now that you have a basic Jekyll blog set up, you can:
- Write your first blog post
- Customize the theme
- Add plugins for additional functionality
- Set up a custom domain

I'll be writing more about Jekyll customization and advanced features in future posts. Stay tuned!

---

*Have questions about setting up Jekyll? Feel free to reach out on [GitHub](https://github.com/josephmalisov) or [Twitter](https://twitter.com/josephmalisov)!* 