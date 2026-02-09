---
title: "Migrating from WordPress to Hugo: A Complete Guide"
date: 2026-02-09T10:00:00Z
draft: false
tags: ["hugo", "wordpress", "github-pages", "migration", "static-site"]
categories: ["web"]
author: "SarCoptU"
description: "A comprehensive guide to migrating your blog from WordPress to Hugo and deploying it on GitHub Pages"
---

After years of running my blog on WordPress, I finally made the switch to Hugo and GitHub Pages. The result? A blazingly fast, version-controlled blog that costs nothing to host. Here's how I did it and what I learned along the way.

## Why I Made the Switch

WordPress is powerful, but over time I found myself frustrated with:

- **Performance issues**: Even with caching plugins, my site felt sluggish
- **Security concerns**: Constant updates and vulnerability patches
- **Hosting costs**: Paying monthly for shared hosting
- **Database complexity**: Backing up and managing MySQL databases
- **Plugin bloat**: Each feature required another plugin

Hugo offered a compelling alternative: static site generation, Git-based workflow, and free hosting on GitHub Pages.

## Prerequisites

Before starting, make sure you have:

- Hugo installed (`brew install hugo` on Mac, or download from [gohugo.io](https://gohugo.io))
- Git installed
- A GitHub account
- Access to your WordPress admin panel

## Step 1: Export Your WordPress Content

First, I needed to get my content out of WordPress.

### Using the WordPress Exporter Plugin

1. Install the "WordPress to Hugo Exporter" plugin (or use the built-in WordPress exporter)
2. Go to Tools → Export in your WordPress admin
3. Select "All content" and download the XML file

For the WordPress to Hugo Exporter plugin:
```bash
# Install via WP-CLI if you prefer
wp plugin install wordpress-to-hugo-exporter --activate
wp hugo-export
```

This creates a ZIP file with your posts in Markdown format, organized by date.

### Alternative: Manual Export with Jekyll Exporter

If the Hugo exporter doesn't work well, you can use the Jekyll exporter and convert later:

```bash
wp plugin install jekyll-exporter --activate
wp jekyll-export
```

## Step 2: Set Up Hugo Locally

Create your new Hugo site:

```bash
# Create a new Hugo site
hugo new site my-blog
cd my-blog

# Initialize git repository
git init

# Add a theme (I chose PaperMod, but there are many options)
git submodule add https://github.com/adityatelange/hugo-PaperMod themes/PaperMod
```

Edit `hugo.toml` (or `config.toml`) to configure your site:

```toml
baseURL = 'https://yourusername.github.io/'
languageCode = 'en-us'
title = 'My Awesome Blog'
theme = 'PaperMod'

[params]
  description = "A blog about technology and life"
  author = "Your Name"
  
[menu]
  [[menu.main]]
    name = "Home"
    url = "/"
    weight = 1
  [[menu.main]]
    name = "Posts"
    url = "/posts/"
    weight = 2
  [[menu.main]]
    name = "About"
    url = "/about/"
    weight = 3
```

## Step 3: Import and Clean Your Content

Copy your exported WordPress posts to Hugo:

```bash
# Create posts directory if it doesn't exist
mkdir -p content/posts

# Copy exported posts
cp -r ~/Downloads/wordpress-export/* content/posts/
```

Now comes the tedious part: cleaning up the content. I had to:

### Fix Front Matter

WordPress posts might have messy front matter. Standardize it:

```yaml
---
title: "Your Post Title"
date: 2024-01-15T10:00:00Z
draft: false
tags: ["tag1", "tag2"]
categories: ["category1"]
---
```

### Convert Image Paths

WordPress uses absolute URLs for images. I needed to:

1. Download all images from WordPress
2. Place them in `static/images/`
3. Update image references in posts:

```bash
# Find and replace image URLs
find content/posts -type f -name "*.md" -exec sed -i '' 's|https://oldsite.com/wp-content/uploads/|/images/|g' {} +
```

### Fix Shortcodes

WordPress shortcodes don't work in Hugo. I replaced them with standard markdown or HTML:

**WordPress:**
```
[gallery ids="1,2,3"]
```

**Hugo (using HTML):**
```html
<div class="gallery">
  <img src="/images/img1.jpg" alt="Image 1">
  <img src="/images/img2.jpg" alt="Image 2">
  <img src="/images/img3.jpg" alt="Image 3">
</div>
```

**Or use standard markdown:**
```markdown
![Image 1](/images/img1.jpg)
![Image 2](/images/img2.jpg)
![Image 3](/images/img3.jpg)
```

## Step 4: Set Up GitHub Repository

Create a repository for your Hugo site:

```bash
# On GitHub, create a new repository named: yourusername.github.io

# Add remote and push
git remote add origin git@github.com:yourusername/yourusername.github.io.git
git add .
git commit -m "Initial commit: Hugo site"
git push -u origin main
```

## Step 5: Configure GitHub Actions for Deployment

Create `.github/workflows/hugo.yml` for automatic deployment:

```yaml
name: Deploy Hugo site to Pages

on:
  push:
    branches:
      - main
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

defaults:
  run:
    shell: bash

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.121.0
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb
      
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0
      
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v4
      
      - name: Build with Hugo
        env:
          HUGO_ENVIRONMENT: production
          HUGO_ENV: production
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"
      
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

## Step 6: Enable GitHub Pages

1. Go to your repository Settings
2. Navigate to Pages (in the sidebar)
3. Under "Source", select "GitHub Actions"
4. Your site will build and deploy automatically

## Step 7: Set Up Custom Domain (Optional)

If you want to use your own domain:

1. Add a `CNAME` file to your `static/` folder:
   ```
   yourdomain.com
   ```

2. Configure DNS records with your domain provider:
   - Add A records pointing to GitHub's IPs:
     - 185.199.108.153
     - 185.199.109.153
     - 185.199.110.153
     - 185.199.111.153
   - Or add a CNAME record: `yourusername.github.io`

3. In GitHub repository settings, add your custom domain

## Step 8: Set Up Redirects

To preserve SEO, I set up redirects from my old WordPress URLs to new Hugo URLs.

Create `static/_redirects` (if your hosting supports it) or use meta refresh:

```html
<!-- In your 404.html or individual posts -->
<meta http-equiv="refresh" content="0; url=/posts/new-url/">
```

For GitHub Pages, I created alias front matter in posts:

```yaml
---
title: "My Post"
aliases:
    - /2024/01/old-wordpress-slug/
---
```

## Lessons Learned

### What Went Well

- **Speed improvement**: My site loads 10x faster
- **Version control**: Every change is tracked in Git
- **Cost savings**: Zero hosting costs
- **Simplicity**: No database, no PHP, just static files
- **Security**: No backend to hack

### Challenges I Faced

- **Time investment**: Content cleanup took longer than expected
- **Learning curve**: Hugo's templating syntax required learning
- **Missing features**: Comments required third-party services (I used Giscus)
- **Build times**: Large sites can be slow to build locally

### Things I'd Do Differently

- Start with a simpler theme to learn Hugo basics
- Automate the content cleanup with scripts
- Set up staging branch for testing before deployment
- Document my custom shortcodes from the start

## Useful Resources

- [Hugo Documentation](https://gohugo.io/documentation/)
- [Hugo Themes](https://themes.gohugo.io/)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [Hugo Discourse Forum](https://discourse.gohugo.io/)

## Conclusion

Migrating from WordPress to Hugo was one of the best technical decisions I've made for my blog. The initial time investment was worth it for the long-term benefits of speed, simplicity, and cost savings.

If you're considering making the switch, my advice is: start small, migrate one category at a time if needed, and don't rush the content cleanup process. Your future self will thank you for taking the time to do it right.

### Post created with AI
---

*Last updated: February 9, 2026*
