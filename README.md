# my_MoneyPrinterTurbo

个人仓库：把开源项目 [MoneyPrinterTurbo](https://github.com/harry0703/MoneyPrinterTurbo) 落到本仓库的 `MoneyPrinterTurbo/` 目录，用来本地生成短视频。

**它不是** Sora / 可灵那种「模型逐帧生成画面」。默认流程是：LLM 写文案 → Pexels 等库存素材 → Edge TTS 配音 → 本机 FFmpeg 合成。

---

## 仓库结构

```text
.
├── README.md                              # 本摘要
├── MoneyPrinterTurbo-部署与Pexels申请.md   # 完整交接（Pexels 申请 + 部署）
└── MoneyPrinterTurbo/                     # 上游项目全文
    ├── UPSTREAM.txt                       # 本次拉取对应的上游 commit
    ├── config.example.toml                # 配置模板（可提交）
    ├── config.toml                        # 本地配置（含 Key，勿提交）
    └── README.md                          # 上游中文说明
```

当前上游提交见 `MoneyPrinterTurbo/UPSTREAM.txt`。

---

## 最低成本组合（大陆）

```text
LLM：DeepSeek 或通义（或 Ollama 本地）
TTS：Edge TTS（免费，不用 Key）
素材：Pexels 免费 API
剪辑：本机 FFmpeg
```

---

## 快速开始

详细步骤（含 Pexels 申请表怎么填）见：[MoneyPrinterTurbo-部署与Pexels申请.md](./MoneyPrinterTurbo-部署与Pexels申请.md)

```bash
cd MoneyPrinterTurbo
cp config.example.toml config.toml
python3.11 -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt -i https://mirrors.aliyun.com/pypi/simple/
sh webui.sh                        # Windows: webui.bat
```

然后在 `config.toml` 或 WebUI「基础设置」里填：

- Pexels API Key（素材）
- 大模型 API Key（写文案；Ollama 可免）

**不要把 Key 提交进 Git。** `config.toml` 已被忽略。

浏览器一般打开 http://127.0.0.1:8501 。
