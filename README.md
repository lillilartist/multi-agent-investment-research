# 多智能体投资研究 · 研究问题生成器（开题器）

> 本仓库是视频 **EP01《多智能体 AI 系统是什么？为什么我用它来做投资研究》** 的配套演示代码。
> 视频里看到的就是这个 notebook。

给定一家公司名称，让 3 位不同视角的分析师 Agent（**价值 / 成长 / 风险**）经过 6 轮互相质疑与辩论，最后由一个综合器 Agent 浓缩出一份**按优先级排好的研究问题清单**。

它**不给投资结论**——它给的是「研究这家公司，你必须先搞清楚哪些问题」。所以叫**开题器**。

```
3 位分析师 × 6 轮辩论 + 1 个综合器 = 19 次 LLM 调用
```

---

## ⚠️ 免责声明

本项目仅为 **AI 多智能体系统的技术演示**，用于说明研究问题如何被提出、拆解和验证。
代码运行产生的任何内容**不构成投资建议**，亦不构成对任何证券、公司或投资策略的推荐、保证或判断。
Agent 在辩论中可能提出极端的压力测试情景，这些只是「值得在后续研究中被考虑的问题」，**不代表任何事实指控**。

---

## 运行前准备

- **Python ≥ 3.10**
- 一把 **Google Gemini API Key**（默认用 Gemini；在 [Google AI Studio](https://aistudio.google.com/apikey) 免费申请）
- 跑一次完整的 6 轮辩论约 **19 次模型调用**，用 Gemini Pro 成本大约 **1–2 美元**（按官方当下价格为准）

> 也可以改用 OpenAI / OpenRouter——见 notebook 第 5 步的注释。

---

## 快速开始

```bash
# 1. 进入本目录
cd 路径/到/这个文件夹

# 2.（推荐）建虚拟环境
python3 -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate

# 3. 装依赖
pip install -U langchain-google-genai langgraph langchain_openai langchain_core python-dotenv

# 4. 配置 API Key：复制模板后填入你自己的 key
cp .env.example .env
# 然后编辑 .env，把 GOOGLE_API_KEY= 后面填上你的 key
```

> notebook 第 1 个代码单元里也带了 `%pip install`，如果你直接在 Jupyter 里跑，可以跳过上面第 3 步。

---

## 怎么跑

用 Jupyter Lab 或 VS Code / Cursor 打开 `research_questions.ipynb`，从上往下逐个单元运行。

要换公司，改 **第 9 步「运行」** 单元里这一行：

```python
COMPANY = ""     # 改成你想研究的公司
```

运行完成后：

- 最终的研究计划在「第 10 步：查看最终结果」单元
- 每轮辩论细节在「第 11 步」单元，并会保存成 `process_<公司>.md`
- 最终结果会保存成 `output_<公司>.md`

> 这两个 `*.md` 是你本地运行的产物，已在 `.gitignore` 里，不会进仓库。

---

## 这是怎么运作的（notebook 章节对应）


| 步骤       | 做什么                                                                                 |
| -------- | ----------------------------------------------------------------------------------- |
| 第 3 步    | 定义共享状态 `ResearchState`（各分析师的历史、轮次、最终问题）                                             |
| 第 4 步    | 4 个角色 system prompt + 每轮的循环模板（思考→提问→回应→更新）                                          |
| 第 6 步    | 实现各节点：3 个分析师节点 + 聚合节点 + 综合器节点                                                       |
| 第 7 步    | 用 [LangGraph](https://github.com/langchain-ai/langgraph) 把节点组装成图：并行启动 → 多轮辩论循环 → 综合 |
| 第 9–12 步 | 运行、查看结果、保存文件                                                                        |


---

## 使用的模型

notebook 默认 `gemini-3.1-pro-preview`（见第 5 步）。模型名和价格会随官方更新变化，跑之前建议确认当下可用的版本。想换更便宜的模型或换厂商，改第 5 步那一行即可。

---

## License

[MIT](./LICENSE)