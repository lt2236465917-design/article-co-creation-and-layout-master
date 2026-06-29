# article-co-creation-and-layout-master

一个跑在 Claude Code 里的 Skill。把「写一篇公众号长文」这件事，从选题一路带到草稿箱，全流程一站式完成。

---

## 这个 Skill 能做什么

简单一句话：**陪你写完一篇公众号文章，并排好版**。

具体覆盖六个阶段：

1. **选题定位** — 帮你聚焦核心观点、目标读者、开篇场景
2. **大纲设计** — 产出文章骨架，让你选字数范围（2000 字到 1 万字以上）
3. **逐章节共创** — 每写完一章停下来等你审，不一次性甩全文
4. **配图标注** — 视频截图标时间戳，AI 配图直接写好生图提示词（含封面图，900x383）
5. **全文审查 + 标题确认** — 扫一遍禁忌项，生成 3-5 版标题让你选
6. **公众号排版** — 自动把 Markdown 排成可直接复制到公众号编辑器的 HTML

最终交付两个版本：
- `article-with-images.md`：带配图标注，本地备份
- `article.md`：纯正文，直接用于排版

---

## 这个 Skill 的优势

**1. 共创，不是代写**

你的决策权始终在你手上。Skill 不会一次性甩出完整文章，而是分阶段推进，每一步等你确认。它的角色是审稿人、提问者、排版工——不是代笔。

**2. 内置一套严格的写作风格 DNA**

写出来的东西不会有 AI 味。Skill 内置了 20+ 条具体禁忌：

- 禁止破折号、英文标点、段尾句号
- 禁止「让我们一起探索」「在当今时代」这类套话
- 禁止「更炸裂的是」「更绝的是」这类夸张递进
- 禁止段尾升华句、自问自答铺垫
- 禁止点赞关注引导
- 段落超 5 句必须拆短

每段写完都会过一遍这个清单。

**3. 配图提示词不糊弄**

普通 AI 写出来的生图提示词长这样：「一张扁平风格的插画，展示 AI 创业的场景，配色以蓝绿为主」——这种放在任何科技文章上都成立，等于没写。

这个 Skill 强制要求六要素结构（画面主体 / 视觉隐喻 / 构图层次 / 色彩方案 / 风格定义 / 尺寸比例），封面图还会自动量化字号比例、强制上下分行排版、强制视觉化标题里的关键名词。

**4. 排版可以直接编辑**

排出来的 HTML 默认带 `contenteditable="true"`，你可以在浏览器里直接改文字，改完全选复制粘进公众号编辑器，排版不会乱。

**5. 整套都是开源代码**

没有黑盒。SKILL.md 里写了所有规则，scripts/ 里是所有的发布脚本，references/ 里是组件库和排版模板。你可以照着改成自己的风格。

---

## 怎么用

### 前置依赖

- [Claude Code](https://docs.claude.com/en/docs/claude-code) — 这个 Skill 跑在 Claude Code 上
- Node.js 18+ — 跑发布脚本要用
- 一个公众号（如果要用一键推送到草稿箱功能）

### 安装

把这个 Skill 放到 Claude Code 的 skills 目录：

```bash
git clone https://github.com/lt2236465917-design/article-co-creation-and-layout-master.git \
  ~/.claude/skills/article-co-creation-and-layout-master
```

装一下 scripts 的依赖：

```bash
cd ~/.claude/skills/article-co-creation-and-layout-master/scripts
npm install
```

### 触发

打开 Claude Code，直接说人话就行：

- "我想写一篇关于 XX 的文章"
- "帮我把这堆素材理成一篇稿子"
- "这篇文章帮我排个公众号版"
- "推送到公众号草稿箱"

Skill 会自动接管全流程。

---

## 想要完全发挥 Skill 的全部功能，需要接入哪些东西

这个 Skill 最基础的写作 + 排版功能开箱即用，**不需要任何额外配置**。但要解锁完整能力，需要按需接入下面这些：

### 1. 微信公众号 API（一键推送到草稿箱）

不接入就只能手动复制粘贴到公众号编辑器（这也能用，只是慢一点）。

需要去公众号后台 → 开发 → 基本配置，拿到：

```env
WECHAT_APP_ID=
WECHAT_APP_SECRET=
```

把 `.env.example` 复制成 `.env` 填上即可。

⚠️ 公众号必须是已认证的，未认证的订阅号没有这个 API 权限。

### 2. OpenAI API（自动生成封面图）

不接入就只能拿到生图提示词，自己去 ChatGPT / Midjourney / 即梦 出图。

```env
OPENAI_API_KEY=
OPENAI_IMAGE_MODEL=gpt-image-2
```

### 3. 飞书云文档（多平台分发）

如果你要把文章同时发布到「人人都是产品经理」「少数派」「即刻」「掘金」这类平台，建议接入飞书 MCP（[lark-doc](https://github.com/larksuite/lark-openapi-mcp)），Skill 会自动把定稿文章导入飞书，带完整排版，复制粘贴到任何平台都能保留格式。

不接入就只能用本地 Markdown 文件，分发到其他平台时排版会丢一部分。

### 4. Web Fetch / Web Search 工具

Claude Code 自带，无需额外配置。Skill 会在你需要补充行业信息时自动调用。

---

## 目录结构

```
article-co-creation-and-layout-master/
├── SKILL.md                          # Skill 主文件（所有规则在这里）
├── .env.example                      # 环境变量模板
├── prompts/
│   └── wechat-format-prompt.md       # 公众号排版提示词
├── references/
│   ├── article-template.html         # 排版骨架（带手机预览框）
│   ├── components.md                 # 组件库（卡片、引用块、表格等）
│   ├── wechat-html-spec.md           # 微信 HTML 规范
│   └── styles/                       # 风格预设
│       ├── tech-card-green.md        # 技术教程风格
│       └── ai-news-signal-green.md   # AI 资讯风格
└── scripts/
    ├── publish.ts                    # 一键推送公众号草稿箱
    ├── render.ts                     # Markdown → HTML 渲染
    ├── wechat.ts                     # 微信 API 封装
    ├── imagegen.ts                   # 封面图生成
    └── img2base64.ts                 # 图片转 base64
```

---

## 许可证

[MIT](LICENSE)。随便用，随便改，随便商用。

---

## 写在最后

这个 Skill 是从我自己写公众号的真实工作流抽出来的。所有禁忌、所有规则、所有排版组件，都是我踩过坑后留下的。

如果你用着用着发现某条规则不适合你，直接改 SKILL.md。这是开源代码，不是黑盒服务。

愿我们永远对世界保持好奇
