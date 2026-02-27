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

## 团队成员排序规则

### 排序顺序
团队成员在网站上按以下顺序显示：

1. **实验室负责人（PI）**：始终排在第一位
2. **在读成员**：按姓名字母顺序排列
3. **已毕业成员（Alumni）**：按离组日期排序，最近离组的排在最前

### 技术实现

#### 实现文件
排序逻辑由以下文件实现：

- **主模板**：`layouts/partials/widgets/portfolio.html`（第87-164行）
- **月份映射**：`layouts/partials/month-to-number.html`

#### 排序逻辑详解

**1. 成员分组**
根据成员标签将成员分为三组：

```hugo
{{ $pi_members := slice }}
{{ $current_members := slice }}
{{ $alumni_members := slice }}
```

- **PI成员**：标签包含 `Principal Investigator`
- **在读成员**：标签不包含 `Principal Investigator` 或 `Alumni`
- **已毕业成员**：标签包含 `Alumni`

**2. 日期提取**
对于已毕业成员，从 `summary` 字段提取离组日期：

```hugo
{{/* 使用正则表达式提取最后一个日期 */}}
{{ $all_matches := findRE "~\\s*([A-Za-z]+\\.\\s*\d{4})" $summary "all" }}
{{ $last_match := index $all_matches (sub (len $all_matches) 1) }}
```

- **日期格式示例**：`__Sep. 2023 ~ Jun. 2025__`
- **正则表达式**：`~\s*([A-Za-z]+\.\s*\d{4})`
- **关键点**：使用 `"all"` 参数获取所有匹配，然后取最后一个（离组日期）

**3. 月份转换**
使用 `month-to-number.html` 部分模板将月份缩写转换为数字：

| 缩写 | 月份 | 数值 |
|------|------|------|
| Jan. | 1月 | 1 |
| Feb. | 2月 | 2 |
| Mar. | 3月 | 3 |
| Apr. | 4月 | 4 |
| May. | 5月 | 5 |
| Jun. | 6月 | 6 |
| Jul. | 7月 | 7 |
| Aug. | 8月 | 8 |
| Sep. | 9月 | 9 |
| Oct. | 10月 | 10 |
| Nov. | 11月 | 11 |
| Dec. | 12月 | 12 |

**4. 可排序日期格式**
将日期转换为 `YYYYMM` 格式以便排序：

```hugo
{{ $sortable_date := printf "%04d%02d" (int $year) $month_num }}
```

- 例如：`Jun. 2025` → `202506`
- 优点：直接按字符串比较即可得到正确的日期顺序

**5. 排序策略**

```hugo
{{/* PI成员：保持原顺序 */}}
{{ $sorted_pi := $pi_members }}

{{/* 在读成员：按姓名字母升序 */}}
{{ $sorted_current := sort $current_members "title" "asc" }}

{{/* 已毕业成员：先按离组日期降序，再按姓名升序 */}}
{{ $sorted_alumni := sort $alumni_members "sortable_date" "desc" }}
{{ $sorted_alumni = sort $sorted_alumni "title" "asc" }}
```

**6. 最终合并**

```hugo
{{/* 按正确顺序合并：PI → 在读 → 已毕业 */}}
{{ $sorted_query := slice }}
{{ range $sorted_pi }}
  {{ $sorted_query = $sorted_query | append .page }}
{{ end }}
{{ range $sorted_current }}
  {{ $sorted_query = $sorted_query | append .page }}
{{ end }}
{{ range $sorted_alumni }}
  {{ $sorted_query = $sorted_query | append .page }}
{{ end }}
```

### 维护指南

#### 添加新成员时

1. **在读成员**：
   - 确保标签不是 `Principal Investigator` 或 `Alumni`
   - `summary` 中日期应包含 `~ Now`，如 `__2024年9月 ~ 至今__`
   - 成员会自动按姓名字母顺序显示

2. **已毕业成员**：
   - 标签设置为 `Alumni`
   - `summary` 中日期格式：`__开始年月 ~ 离组年月__`
   - 示例：`__2022年9月 ~ 2024年6月__`
   - 成员会按离组日期自动排序

#### 成员离组时

1. 将标签从原角色（如 `Undergraduate`）改为 `Alumni`
2. 更新 `summary` 中的时间范围：
   - 修改离组日期，如 `__2024年9月 ~ 至今__` → `__2024年9月 ~ 2026年1月__`
3. 确保日期格式符合要求（月份缩写 + 年份）
4. 重新构建网站：`blogdown::build_site()`

#### 常见问题

**Q: 为什么成员排序不正确？**

A: 检查以下几点：
1. 标签是否正确（`Principal Investigator`、`Alumni`）
2. `summary` 中日期格式是否正确
3. 月份是否使用英文缩写（如 `Sep.` 而非 `九月`）
4. 是否重新构建了网站

**Q: 如何修改成员的显示顺序？**

A: 不同类型的成员有不同的排序方式：
- **PI**：无法修改，始终在第一位
- **在读成员**：修改 `title` 字段中的姓名
- **已毕业成员**：修改 `summary` 中的离组日期

**Q: 支持哪些日期格式？**

A: 目前支持的格式：
- `__Sep. 2023 ~ Jun. 2025__`（日期范围）
- `__Sep. 2024 ~ 至今__`（在读成员）
- 月份必须使用英文缩写并带点号（如 `Sep.`）

### 示例

#### 在读成员示例

```yaml
---
tags:
- Undergraduate
title: 张三
summary: __2024年9月 ~ 至今__ <br/> 计算机科学与技术专业
---
```

#### 已毕业成员示例

```yaml
---
tags:
- Alumni
title: 李四
summary: __2021年9月 ~ 2024年6月__ <br/> 获得硕士学位
---
```

### 技术细节

#### 边界情况处理

1. **PI 成员无日期**：分配 `sortable_date = "00000000"` 确保排在第一位
2. **在读成员**：分配 `sortable_date = "99999999"` 确保排在过去日期之后
3. **日期格式错误**：`month-to-number.html` 返回 `0`，需要手动检查
4. **相同离组日期**：按姓名字母顺序排列

#### 性能优化

- 使用单次 `findRE` 调用获取所有日期匹配
- 使用 `YYYYMM` 格式避免复杂日期比较
- 三组独立处理减少排序复杂度

#### 扩展性

- 支持多个 PI（虽然当前只有一个）
- 支持添加新的角色标签
- 支持国际化和不同日期格式（需修改正则表达式）

### 参考文档

完整的技术实现细节和历史记录可在以下位置找到：

- 实现证据：`.sisyphus/evidence/` 目录
- 学习笔记：`.sisyphus/notepads/alumni-sorting/learnings.md`
- 相关任务文档：`.sisyphus/plans/alumni-sorting.md`

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
	
	