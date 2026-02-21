# Chuan-Peng Lab Website

> Website: https://chuan-peng-lab.netlify.app/

---

## Quick Start

1. Clone this repo
2. Run `blogdown::serve_site()` in R console to preview locally
3. Edit files in `content/en/` (English) or `content/zh/` (Chinese)
4. Run `blogdown::build_site()` to build
5. Commit and push to GitHub

---

## Environment Setup

### Prerequisites
- R and RStudio installed
- Git installed

### Install blogdown
```r
install.packages("blogdown")
blogdown::install_hugo()
```

### Local Preview
```r
blogdown::serve_site()   # Start local server
blogdown::stop_site()    # Stop server
blogdown::build_site()   # Build site
```

If changes don't appear:
1. Run `blogdown::stop_site()`, then `blogdown::serve_site()`
2. Or restart RStudio

---

## Files and Folders Overview

```
config/_default/
  config.yaml      # Site title, baseURL
  menus.yaml       # Navigation menu (EN)
  menus_zh.yaml    # Navigation menu (ZH)
  params.yaml      # Theme settings

content/en/        # English content
content/zh/        # Chinese content
  authors/admin/   # PI introduction
  home/            # Homepage sections
  post/            # Blog posts
  project/         # Team members
  publication/     # Publications

static/            # Static files (images, etc.)
```

---

## Update Member Information

### File Paths
- English: `content/en/project/{initials}/index.md`
- Chinese: `content/zh/project/{initials}/index.md`
- Avatar: `content/en/project/{initials}/featured.jpg`

> `{initials}` = name abbreviation, e.g., `csy`, `hcp`

### Role Tags
| Tag | Description |
|-----|-------------|
| `Principal Investigator` | PI / Lab head |
| `Research Assistants` | Research Assistant |
| `Postgraduate` | Graduate student |
| `Undergraduate` | Undergraduate |
| `Alumni` | Former member |

### Add New Member

1. **Create folder**
   - Copy an existing member folder
   - Rename to member's initials (e.g., `csy2` if initials conflict)

2. **Edit `index.md`**
   ```yaml
   ---
   date: "2024-09-01T00:00:00Z"
   image:
     caption: Your Name
     focal_point: Smart
   links:
   - icon: github
     icon_pack: fab
     name: Follow
     url: https://github.com/yourusername
   summary: __Sep. 2024 ~ Now__ <br/> Brief introduction
   tags:
   - Undergraduate        # Choose from role tags above
   title: Your Name
   ---
   Your bio here. Markdown and HTML supported.
   ```

3. **Add avatar**
   - Replace `featured.jpg` (recommended size: < 500KB)

4. **Repeat for both EN and ZH versions**

### Edit Existing Member
- Open `index.md` in the member's folder
- Update fields as needed
- Run `blogdown::serve_site()` to preview

### Member Departure
- Change tag to `Alumni`
- Update `summary` with date range (e.g., `__Sep. 2022 ~ Jun. 2024__`)

---

## Update Publications

### File Paths
- English: `content/en/publication/{year_Publication_Name}/`
- Chinese: `content/zh/publication/{year_Publication_Name}/`

Each publication folder contains:
- `index.md` - Publication metadata
- `cite.bib` - BibTeX citation
- `featured.jpg` - Preview image (optional)

### Add New Publication

1. **Create folder**
   - Copy an existing publication folder
   - Rename to `{Year}_Publication_{FirstAuthor}` (e.g., `2024_Publication_Andres`)

2. **Edit `cite.bib`**
   - Copy BibTeX from Google Scholar or Zotero

3. **Edit `index.md`**

   Key fields:
   ```yaml
   ---
   abstract: Paper abstract (no colons!)
   authors:
   - First Author
   - Second Author
   date: "2024-03-27T00:00:00Z"
   doi: "10.xxxx/xxxxx"
   featured: false        # Show on homepage
   projects:
   - Publication_Andres_2024   # Link to project, optional
   publication: In *Journal Name*
   publication_short: In *J. Name*
   publication_types:
   - "2"                  # 1=Conference, 2=Journal, 3=Preprint
   summary: Brief summary for homepage
   title: Paper Title
   url_pdf: "https://..."  # PDF link, optional
   ---
   ```

4. **Add preview image** (optional)
   - Replace `featured.jpg` with figure from paper

5. **Repeat for ZH version**

### The `projects` Field

The `projects` field links publications to related projects or members.

- **Purpose**: Associate a publication with a specific project page
- **Format**: List of project identifiers
- **Example**:
  ```yaml
  projects:
  - Publication_Andres_2024
  ```
- **Note**: This is optional. Leave empty (`projects: ""`) if not needed.

### Publication Types
| Value | Type |
|-------|------|
| `"1"` | Conference paper |
| `"2"` | Journal article |
| `"3"` | Preprint |

---

## Common Issues

### Site doesn't update after editing
1. Stop server: `blogdown::stop_site()`
2. Restart: `blogdown::serve_site()`
3. If still not working, restart RStudio

### Image not showing
- Check file path is correct
- For new images, place in `static/` folder
- Reference as `/img/filename.jpg` in markdown

### YAML parsing error
- Check for colons in `abstract` field
- Ensure proper indentation
- Use quotes around values with special characters

### Folder name conflict
If two members have same initials:
- Add numeric suffix: `csy`, `csy2`

---

## Checklists

### New Member
- [ ] Create folder in `content/en/project/{initials}/`
- [ ] Create folder in `content/zh/project/{initials}/`
- [ ] Edit `index.md` (EN and ZH)
- [ ] Add `featured.jpg` avatar
- [ ] Set correct role tag
- [ ] Preview with `blogdown::serve_site()`
- [ ] Build with `blogdown::build_site()`
- [ ] Commit and push to GitHub

### New Publication
- [ ] Create folder in `content/en/publication/{year_Publication_Name}/`
- [ ] Create folder in `content/zh/publication/{year_Publication_Name}/`
- [ ] Edit `cite.bib`
- [ ] Edit `index.md` (EN and ZH)
- [ ] Add `featured.jpg` (optional)
- [ ] Set correct `publication_types`
- [ ] Preview with `blogdown::serve_site()`
- [ ] Build with `blogdown::build_site()`
- [ ] Commit and push to GitHub

### Member Departure
- [ ] Update tag to `Alumni` (EN and ZH)
- [ ] Update `summary` with date range
- [ ] Update any ongoing projects info
- [ ] Preview and verify
- [ ] Commit and push to GitHub

---

## Modify Website Introduction

PI introduction is located at:
- `content/en/authors/admin/_index.md`
- `content/zh/authors/admin/_index.md`

Edit the file directly. Content below `---` supports HTML.

---

## Additional Resources

- [Wowchemy Documentation](https://wowchemy.com/docs/)
- [blogdown Book](https://bookdown.org/yihui/blogdown/)
- [Hugo Documentation](https://gohugo.io/documentation/)

---

*Last updated: February 2026*
	
	