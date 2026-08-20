# MoneyPrinterTurbo：部署与 Pexels 申请（交接说明）

> 用途：换仓库后可独立阅读。不含任何真实 API Key。  
> 上游项目：https://github.com/harry0703/MoneyPrinterTurbo  
> 本仓库代码目录：`MoneyPrinterTurbo/`（上游提交见该目录内 `UPSTREAM.txt`）

---

## 1. 项目是什么（先消除误会）

MoneyPrinterTurbo **不是** Sora / 可灵 / Seedance 那种「模型直接生成每一帧」。

默认流水线是：

| 环节 | 默认做法 | 费用 |
|------|----------|------|
| 写文案、提炼搜素材关键词 | 调用 LLM（DeepSeek / 通义 / 智谱 / Kimi 等，也可用本地 Ollama） | 一条文案通常几分钱；Ollama 则免费 |
| 画面 | 从 **Pexels / Pixabay / Coverr** 搜现成高清素材，或用本地视频 | Pexels 免费 Key；也可用 `local` |
| 配音 | **Edge TTS**（WebUI 里有时显示为 Azure TTS V1） | **免费，不用 Key** |
| 字幕 | TTS 时间戳（默认）或本地 Whisper | 免费 |
| 合成 | 本机 FFmpeg | 电费 |

软件本身 MIT 开源、不收费。贵的是你**主动**去接付费 TTS、付费素材、或可灵/Sora 这类视频生成模型。默认可以不接。

大陆最低成本组合建议：

```text
LLM：DeepSeek 或通义（或 Ollama 本地）
TTS：Edge TTS
素材：Pexels 免费 API
剪辑：本机
```

---

## 2. Pexels 是国内还是国外？

**国外网站**（德国创立，现属 Canva），不是国内备案站点。

| 项 | 地址 / 说明 |
|----|-------------|
| 官网 | https://www.pexels.com |
| 申请 API Key | https://www.pexels.com/api/ |
| 接口 | `https://api.pexels.com` |
| 费用 | 免费；默认有频率限制（大约每小时 200 次） |
| 国内访问 | 多数时候能打开；走 Cloudflare，偶发验证码、偏慢 |
| 登录建议 | **用邮箱注册**。Google / Facebook 登录在大陆经常失败 |

打不开官网时：换网络/节点；或改用 Pixabay（同样是国外免费库，https://pixabay.com/api/docs/）；或把素材源改成本地视频 `local`。

---

## 3. 注册时弹窗「What do you create...」怎么选

问卷，不影响能不能拿到 Key。

- **必勾：** Videos  
- **可再勾：** Social Media（短视频常发社交平台）  
- Emails / Ads / Print 等不必勾  

勾完点 **Continue**。右上角 X 关掉一般也能继续。

---

## 4. 「Generate a Pexels API Key」表单怎么填

### Project Name（必填）

```text
MoneyPrinterTurbo
```

### Project Category（必填）

下拉里优先选：`Personal` / `Personal use` / `Software` / `Desktop app` / `Other`。  
选最接近「个人本地软件」的一项，**不要**选 Wallpaper app。

### Explain briefly how and where you want to integrate...（必填）

用途说明通常要求大约 50 个英文字符，整段粘贴：

```text
I use Pexels videos in a local open-source tool called MoneyPrinterTurbo.
The app searches stock footage, adds narration and subtitles, and renders
short educational clips on my own computer. Content is for personal use
and is not resold as a stock or wallpaper service.
```

### URL of your website, app, etc.（可选）

有仓库可填仓库地址，例如：

```text
https://github.com/sclzpangyu/my_MoneyPrinterTurbo
```

纯本地使用可以留空。

### 条款

勾选：I agree to the Terms of Service and API Guidelines.

按钮变亮后点 **Generate API Key**。Key 立刻显示。每个账号通常只有一把，复制保存。

---

## 5. Key 填到项目哪里

**不要把 Key 写进 Git、Issue、PR、群聊。**  
本项目已忽略 `MoneyPrinterTurbo/config.toml`，只要不要手动把它加入提交。

本机：

```bash
cd MoneyPrinterTurbo
cp config.example.toml config.toml
```

打开 `config.toml`，找到并改成（英文双引号，中间不要空格、不要换行）：

```toml
video_source = "pexels"
pexels_api_keys = ["在这里粘贴Pexels的Key"]
```

也可以等 WebUI 启动后，在「基础设置」里粘贴，效果相同。

若改用 Pixabay：

```toml
video_source = "pixabay"
pixabay_api_keys = ["Pixabay的Key"]
```

若用本地素材：

```toml
video_source = "local"
```

---

## 6. 本地部署（Key 准备好之后）

环境：Python 3.11+；Windows 10 / macOS 11 / 主流 Linux。GPU 非必须。

### 6.1 安装依赖（国内镜像）

```bash
cd MoneyPrinterTurbo
python3.11 -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt -i https://mirrors.aliyun.com/pypi/simple/
```

官方也推荐 `uv`：

```bash
cd MoneyPrinterTurbo
uv python install 3.11
uv sync --frozen
```

### 6.2 启动 WebUI

```bash
# macOS / Linux
sh webui.sh

# Windows
webui.bat
```

浏览器打开提示地址，一般为 http://127.0.0.1:8501 。空白页可换 Chrome / Edge。

### 6.3 还缺什么才能出片

| 项 | 是否必须 | 说明 |
|----|----------|------|
| Pexels Key | 用在线素材时必须 | 见上文 |
| 大模型 API Key | 必须（除非 Ollama） | 写文案；DeepSeek / 通义 / 智谱 / Kimi 均可 |
| Edge TTS | 默认即可 | 免费，不用 Key |
| ffmpeg | 一般会自动处理 | 报错时再手动装并在 config 里指定路径 |

在 WebUI「基础设置」里选 LLM 提供商并填 Key。不要用 Ollama 地址去填国产云 API。

### 6.4 命令行出片（可选）

```bash
uv run python cli.py --video-subject "人工智能如何改变日常生活"
```

---

## 7. 本仓库里的文件位置

```text
仓库根目录/
├── README.md                              # 摘要版说明
├── MoneyPrinterTurbo-部署与Pexels申请.md   # 本文件（完整交接）
└── MoneyPrinterTurbo/                     # 上游项目全文
    ├── UPSTREAM.txt                       # 拉取时的上游 commit
    ├── config.example.toml                # 配置模板（可提交）
    ├── config.toml                        # 本地配置（含 Key，勿提交）
    ├── README.md                          # 上游中文说明
    ├── webui.sh / webui.bat
    └── ...
```

换仓库时：把 `MoneyPrinterTurbo/` 目录和本 md 一起带走即可。不要带走已填 Key 的 `config.toml`（到新环境重新 `cp config.example.toml config.toml` 再填）。

---

## 8. 安全注意

- API Key 是密钥，泄露后别人能用你的额度打 Pexels。
- 已在聊天里发过完整 Key 的，若仓库公开或对话可能外传，建议在 Pexels 再生成新 Key，旧 Key 作废。
- 截图请打码 Key 中间几位。

---

## 9. 换仓库后建议顺序

1. 复制本 md + `MoneyPrinterTurbo/` 到新仓库  
2. 确认 Pexels Key 已申请并只存在本地 `config.toml`  
3. 再配一把国产 LLM Key  
4. 按第 6 节启动 WebUI，先生成一条 15～20 秒测试片  

完整上游文档：`MoneyPrinterTurbo/README.md`
