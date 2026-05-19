# Ollama — 本地运行大模型的开源利器

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Ollama Inc. |
| 首次发布 | 2023 年 |
| 用途 | 在本地一键运行和管理大语言模型（LLM），无需 GPU 也能用，完全离线 |

## 🎯 用来做什么？

- **本地运行 LLM**：一条命令下载并运行 Llama、Mistral、Qwen、DeepSeek 等主流模型，数据不出本机
- **开发调试 AI 应用**：替代 OpenAI API，本地跑兼容的 API 服务，开发和测试 LLM 应用零成本
- **嵌入向量生成**：运行 embedding 模型（如 nomic-embed-text），为 RAG 应用提供本地向量
- **实验和对比**：在不同模型间快速切换，对比输出质量、速度和参数量

## 🔧 常用命令

```bash
# 查看所有已下载的模型
ollama list

# 下载一个模型（以 llama3.2 为例）
ollama pull llama3.2

# 运行一个模型并进入交互对话模式
ollama run llama3.2

# 直接给一句话，不进入交互模式
ollama run llama3.2 "用一句话解释量子计算"

# 停止正在运行的模型
ollama stop llama3.2

# 删除一个模型
ollama rm llama3.2

# 查看当前正在运行的模型
ollama ps

# 启动 Ollama 服务（后台常驻，默认监听 127.0.0.1:11434）
ollama serve
```

## 💡 日常使用示例

### 示例 1：下载并对话一个模型

```bash
# 1. 下载模型（以轻量的 llama3.2:1b 为例，约 800MB）
ollama pull llama3.2:1b

# 输出示例
pulling manifest
pulling 748cb1af1b2a... 100% ▕█████████████████████▏ 792 MB
pulling 316bb753ecc5... 100% ▕█████████████████████▏ 1.0 KB
verifying sha256 digest
writing manifest
success

# 2. 进入交互对话
ollama run llama3.2:1b

# 在 >>> 提示符后输入问题
>>> 什么是递归函数？

递归函数就是一个调用自身的函数。
例如计算阶乘：
  def factorial(n):
      return 1 if n == 0 else n * factorial(n-1)

>>> /exit   # 退出对话
```

### 示例 2：用 Ollama 做本地 API 替代 OpenAI

启动服务后，可以用 curl 调用兼容 OpenAI API 的接口：

```bash
# 1. 启动服务（后台运行）
ollama serve &
# 输出: ollama is running at http://127.0.0.1:11434

# 2. 用 curl 调用 chat 接口
curl http://localhost:11434/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3.2:1b",
    "messages": [{"role": "user", "content": "1+1=?"}]
  }'

# 返回示例
{
  "id": "chatcmpl-xxx",
  "object": "chat.completions",
  "model": "llama3.2:1b",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "1+1=2"
      }
    }
  ]
}
```

### 示例 3：使用 embedding 模型生成文本向量

```bash
# 1. 下载 embedding 模型
ollama pull nomic-embed-text

# 2. 用 API 生成向量
curl http://localhost:11434/api/embed \
  -H "Content-Type: application/json" \
  -d '{
    "model": "nomic-embed-text",
    "input": "今天天气真好"
  }' | jq '.embeddings[0] | length'

# 输出: 768  (nomic-embed-text 生成的向量维度为 768)
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| `ollama` 命令找不到 | 未安装 Ollama | `brew install ollama`（macOS）或从 [ollama.com](https://ollama.com) 下载安装包 |
| 模型下载慢或失败 | 默认从国外 registry 拉取 | 检查网络连接，或使用代理；模型文件较大（数百 MB 到数 GB），需耐心等待 |
| `ollama run` 后输出很慢 | 模型在 CPU 上运行 | Ollama 优先使用 GPU，macOS 上会自动利用 Apple Silicon (M 系列芯片)；如果只有 Intel CPU，7B 以上模型会比较慢 |
| 运行大模型时内存不足 | 模型参数太大，超出可用内存 | 改用小模型（如 `llama3.2:1b` 或 `qwen2.5:0.5b`），或关闭其他应用释放内存 |
| API 调用返回 404 | Ollama 服务未启动 | 先运行 `ollama serve`，确认 `http://localhost:11434` 可访问 |
| macOS 上 M1/M2/M3 芯片运行比预期慢 | 未启用 Metal 加速 | 确保 Ollama 版本 >= 0.1.0，Apple Silicon 默认自动启用 Metal |
| 两个模型同时运行时变慢 | 共享显存/内存 | 先 `ollama stop <model>` 停止不用的模型，或一次只运行一个 |

> **macOS 特别说明**：Ollama 对 Apple Silicon（M 系列芯片）有原生 Metal 加速支持，在 MacBook 上跑 7B 以下模型体验极佳。Intel Mac 在 CPU 上跑大模型会明显慢一些。

## 🔗 参考链接

- [Ollama 官网](https://ollama.com/)
- [Ollama GitHub 仓库](https://github.com/ollama/ollama)
- [Ollama 支持的模型列表](https://ollama.com/library)
- [Ollama API 文档](https://github.com/ollama/ollama/blob/main/docs/api.md)
