# GitHub 个人主页改造记录

日期：2026-10-15。目标账号：`2855313278`。

## 范围与体检

- 工作区原有 `AGENTS.md` 和操作指令，没有项目代码、依赖或测试。
- 公开主页没有公开仓库，账号未填写姓名、简介及技术栈。
- 本次任务为个人主页装修；不执行指令示例中的第三方项目搜索和重构。
- 在独立目录 `2855313278/` 初始化 Git，开工分支为 `refactor/auto`。
- 采用 GitHub 原生 README、HTML 表格和自带 SVG，不引入构建工具或第三方图片服务。

## 设计决策

- 风格：墨绿背景、薄荷绿点缀、网格与轨道图形；双语文案。
- 内容：账号、欢迎语、探索／实践／沉淀，以及项目、收藏、动态入口。
- 不根据工作区名称或操作指令推断用户的学历、职业、技术能力和项目成果。
- 暂不展示空项目卡片、技能徽章或依赖外部服务的统计图片。
- 仅新增文件，没有替换或删除原有工作区文件，也没有使用第三方项目源码。

## 逐文件修改与验证

### `assets/header.svg`

原创矢量横幅，包含背景网格、轨道图形、账号及双语标语。README 使用相对路径引用。

验证：XML 解析成功；画布为 `1280 × 420`；没有脚本、外链字体或外部图片。

```powershell
python -c "import xml.etree.ElementTree as E; r=E.parse('assets/header.svg').getroot(); assert r.attrib['viewBox']=='0 0 1280 420'; assert not any(e.tag.endswith(('script','image')) for e in r.iter()); print('PASS')"
```

### `README.md`

新增个人主页正文。导航在手机宽度下使用简短中文标签，减少换行。

验证：UTF-8 正常；引用图片存在；导航链接指向目标账号；GitHub Markdown API 成功渲染；桌面和手机截图已人工检查，横幅、表格和文字无截断，页面无横向溢出。

```powershell
python -c "from pathlib import Path; p=Path('README.md'); assert p.read_text(encoding='utf-8'); assert Path('assets/header.svg').is_file(); print('PASS')"
$payload = @{text = (Get-Content -Raw -Encoding utf8 README.md); mode = 'gfm'} | ConvertTo-Json
$rendered = Invoke-WebRequest -Uri 'https://api.github.com/markdown' -Method Post -ContentType 'application/json; charset=utf-8' -Body ([Text.Encoding]::UTF8.GetBytes($payload))
$rendered.StatusCode # 200
```

截图视口：桌面 `1120px`，手机 `390px`。预览使用 GitHub API 渲染的正文和模拟 GitHub 样式；线上主题、字体和容器宽度可能不同。

### `LOG.md`

记录设计依据、修改范围、可重复验证的命令及登录连接问题。

```powershell
git diff --check
git log --oneline
git status --short
```

## 遇到的问题

- PowerShell 与 Python 正则引号嵌套导致第一次验证命令未执行；改用 PowerShell 单引号 here-string 输入 Python，验证通过后提交。
- GitHub CLI 已安装但不在 PATH，使用 `C:\Program Files\GitHub CLI\gh.exe` 调用。
- GitHub CLI 起初未登录；直接连接设备授权接口超时。读取系统已启用的代理设置，在该进程中设置代理后，成功取得浏览器授权验证码。
- 发布需要账号本人完成 GitHub 浏览器授权。此记录不代表远端已发布，远端状态以实际仓库为准。

## 发布约定

- 目标为公开同名仓库 `2855313278/2855313278`，主页文件名为 `README.md`。
- 发布前核对登录账号，保留本地 `refactor/auto` 分支；发布到远端 `main`。
- 不覆盖已有仓库内容，不使用强制推送；如遇已有内容，先检查再合并。

一句话特点：以原创 SVG 横幅和轻量双语 README 构建不依赖外部图片服务的 GitHub 个人主页。
