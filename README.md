# 传鹏实验室网站

> 网站地址：https://chuan-peng-lab.netlify.app/

---

## 快速入门

1. 克隆本仓库
2. 在 R 控制台运行 `blogdown::serve_site()` 本地预览
3. 编辑 `content/en/`（英文）或 `content/zh/`（中文）中的文件
4. 运行 `blogdown::build_site()` 构建网站
5. 提交并推送到 GitHub

---

## 环境配置

### 前置条件
- 已安装 R 和 RStudio
- 已安装 Git

### 安装 blogdown
```r
install.packages("blogdown")
blogdown::install_hugo()
```

### 本地预览
```r
blogdown::serve_site()   # 启动本地服务器
blogdown::stop_site()    # 停止服务器
blogdown::build_site()   # 构建网站
```

如果修改未生效：
1. 运行 `blogdown::stop_site()`，再运行 `blogdown::serve_site()`
2. 或重启 RStudio

---

## 文件结构概览

```
config/_default/
  config.yaml      # 网站标题、地址
  menus.yaml       # 导航菜单（英文）
  menus_zh.yaml    # 导航菜单（中文）
  params.yaml      # 主题设置

content/en/        # 英文内容
content/zh/        # 中文内容
  authors/admin/   # PI 介绍
  home/            # 首页模块
  post/            # 博客文章
  project/         # 团队成员
  publication/     # 发表论文

static/            # 静态文件（图片等）
```

---

## 更新成员信息

### 文件路径
- 英文：`content/en/project/{姓名缩写}/index.md`
- 中文：`content/zh/project/{姓名缩写}/index.md`
- 头像：`content/en/project/{姓名缩写}/featured.jpg`

> `{姓名缩写}` 即姓名首字母，如 `csy`、`hcp`

### 角色标签
| 标签 | 说明 |
|-----|------|
| `Principal Investigator` | 实验室负责人 |
| `Research Assistants` | 研究助理 |
| `Postgraduate` | 研究生 |
| `Undergraduate` | 本科生 |
| `Alumni` | 已毕业成员 |

### 添加新成员

1. **创建文件夹**
   - 复制一个现有成员的文件夹
   - 重命名为新成员的姓名缩写（如重名可加数字后缀，如 `csy2`）

2. **编辑 `index.md`**
   ```yaml
   ---
   date: "2024-09-01T00:00:00Z"
   image:
     caption: 你的名字
     focal_point: Smart
   links:
   - icon: github
     icon_pack: fab
     name: Follow
     url: https://github.com/你的用户名
   summary: __2024年9月 ~ 至今__ <br/> 简短介绍
   tags:
   - Undergraduate        # 从上方角色标签中选择
   title: 你的名字
   ---
   在这里写个人简介，支持 Markdown 和 HTML。
   ```

3. **添加头像**
   - 替换 `featured.jpg`（建议大小 < 500KB）

4. **英文和中文版本都要添加**

### 编辑现有成员
- 打开成员文件夹中的 `index.md`
- 根据需要更新字段
- 运行 `blogdown::serve_site()` 预览

### 成员离组
- 将标签改为 `Alumni`
- 更新 `summary` 中的时间范围（如 `__2022年9月 ~ 2024年6月__`）

---

## 更新论文信息

### 文件路径
- 英文：`content/en/publication/{年份_Publication_第一作者}/`
- 中文：`content/zh/publication/{年份_Publication_第一作者}/`

每个论文文件夹包含：
- `index.md` - 论文元数据
- `cite.bib` - BibTeX 引用
- `featured.jpg` - 预览图片（可选）

### 添加新论文

1. **创建文件夹**
   - 复制一个现有论文文件夹
   - 重命名为 `{年份}_Publication_{第一作者}`（如 `2024_Publication_Andres`）

2. **编辑 `cite.bib`**
   - 从 Google Scholar 或 Zotero 复制 BibTeX

3. **编辑 `index.md`**

   关键字段：
   ```yaml
   ---
   abstract: 论文摘要（注意不能有冒号！）
   authors:
   - 第一作者
   - 第二作者
   date: "2024-03-27T00:00:00Z"
   doi: "10.xxxx/xxxxx"
   featured: false        # 是否在主页显示
   projects:
   - Publication_Andres_2024   # 关联项目，可选
   publication: In *Journal Name*
   publication_short: In *J. Name*
   publication_types:
   - "2"                  # 1=会议论文, 2=期刊论文, 3=预印本
   summary: 主页显示的简介
   title: 论文标题
   url_pdf: "https://..."  # PDF 链接，可选
   ---
   ```

4. **添加预览图片**（可选）
   - 用论文中的图片替换 `featured.jpg`

5. **中文版同样操作**

### `projects` 字段

`projects` 字段用于将论文与相关项目或成员关联。

- **用途**：将论文与特定项目页面关联
- **格式**：项目标识符列表
- **示例**：
  ```yaml
  projects:
  - Publication_Andres_2024
  ```
- **注意**：这是可选的。如不需要可留空（`projects: ""`）

### 论文类型
| 值 | 类型 |
|-------|------|
| `"1"` | 会议论文 |
| `"2"` | 期刊论文 |
| `"3"` | 预印本 |

---

## 常见问题

### 修改后网站未更新
1. 停止服务器：`blogdown::stop_site()`
2. 重新启动：`blogdown::serve_site()`
3. 如仍无效，重启 RStudio

### 图片不显示
- 检查文件路径是否正确
- 新图片放在 `static/` 文件夹
- 在 markdown 中引用为 `/img/filename.jpg`

### YAML 解析错误
- 检查 `abstract` 字段是否有冒号
- 确保缩进正确
- 含特殊字符的值用引号包裹

### 文件夹名冲突
如果两人姓名缩写相同：
- 添加数字后缀：`csy`、`csy2`

---

## 检查清单

### 新成员加入
- [ ] 在 `content/en/project/{姓名缩写}/` 创建文件夹
- [ ] 在 `content/zh/project/{姓名缩写}/` 创建文件夹
- [ ] 编辑 `index.md`（英文和中文）
- [ ] 添加 `featured.jpg` 头像
- [ ] 设置正确的角色标签
- [ ] 用 `blogdown::serve_site()` 预览
- [ ] 用 `blogdown::build_site()` 构建
- [ ] 提交并推送到 GitHub

### 新论文发表
- [ ] 在 `content/en/publication/{年份_Publication_第一作者}/` 创建文件夹
- [ ] 在 `content/zh/publication/{年份_Publication_第一作者}/` 创建文件夹
- [ ] 编辑 `cite.bib`
- [ ] 编辑 `index.md`（英文和中文）
- [ ] 添加 `featured.jpg`（可选）
- [ ] 设置正确的 `publication_types`
- [ ] 用 `blogdown::serve_site()` 预览
- [ ] 用 `blogdown::build_site()` 构建
- [ ] 提交并推送到 GitHub

### 成员离组
- [ ] 将标签改为 `Alumni`（英文和中文）
- [ ] 更新 `summary` 中的时间范围
- [ ] 更新进行中项目的信息
- [ ] 预览并确认
- [ ] 提交并推送到 GitHub

---

## 修改网站简介

PI 介绍位于：
- `content/en/authors/admin/_index.md`
- `content/zh/authors/admin/_index.md`

直接编辑文件即可。`---` 分隔线下方支持 HTML。

---

## 其他资源

- [Wowchemy 文档](https://wowchemy.com/docs/)
- [blogdown 书籍](https://bookdown.org/yihui/blogdown/)
- [Hugo 文档](https://gohugo.io/documentation/)

---

*最后更新：2026年2月*
	
	