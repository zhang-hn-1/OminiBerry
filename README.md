# OminiBerry 草莓病害诊断与防治决策支持系统

OminiBerry 是一个面向草莓病害场景的多智能体诊断原型。系统接收草莓叶片、花器或果实图片，结合视觉摘要、知识检索与多轮专家协作推理，输出结构化诊断结果、处置建议与 Markdown 报告。

## 项目特点

- 面向草莓病害的诊断与防治决策流程
- 前端上传图片，后端自动生成诊断报告
- 多专家协同分析：病理推理、鉴别排除、防治建议、栽培管理
- 支持本地运行、运行记录、病例库与知识库沉淀
- 当前默认配置为无 DINO 的轻量模式，可以直接结合本地 Ollama 运行

## 当前草莓类目

`classes.txt` 当前使用以下类别：

- `leaf`
- `leaf_spot`
- `powdery_mildew_leaf`
- `gray_mold`
- `angular_leafspot`
- `blossom_blight`
- `powdery_mildew_fruit`
- `anthracnose_fruit_rot`

## 运行方式

```powershell
.\.venv\Scripts\python.exe -m uvicorn app.api.main:app --host 0.0.0.0 --port 8000
```

若使用本地 Ollama，请在 `.env` 中配置：

```env
MULTIAGENT_LLM=ollama
OLLAMA_BASE_URL=http://127.0.0.1:11434
OLLAMA_MODEL=deepseek-r1:8b
ENABLE_LOCAL_DINOV3=false
ENABLE_LOCAL_QWEN3_VL=false
```

## 主要入口

- `app/api/main.py`
- `app/api/routes_diagnosis.py`
- `app/core/pipeline/diagnosis_pipeline.py`
- `app/core/agents/prompts.py`
- `app/core/agents/report_packet.py`

## 说明

- 当前仓库已经调整为草莓版本，并默认采用无 DINO 运行模式。
- 若后续需要接入真实视觉模型，请保证模型输出类别顺序与 `classes.txt` 保持一致。
- 若本地 Ollama 模型响应过慢，四个专家可能退化为 fallback 输出，建议更换更稳定的本地模型。

## Quick Start

1. Install dependencies:

```powershell
python -m pip install -r requirements.txt
```

2. Configure `.env`:

- `MULTIAGENT_LLM=openai`
- `BASELINE_OPENAI_API_KEY=...`
- `MULTIAGENT_OPENAI_API_KEY=...` optional, falls back to the baseline key if omitted
- `ENABLE_LOCAL_QWEN3_VL=false`
- `ENABLE_LOCAL_DINOV3=false`

DNXAPI is also supported as an OpenAI-compatible provider:

- `MULTIAGENT_LLM=dnxapi`
- `BASELINE_LLM_PROVIDER=dnxapi` optional, if you want the baseline report path to use DNXAPI too
- `DNXAPI_BASE_URL=...`
- `DNXAPI_API_KEY=...`
- `DNXAPI_MODEL=...`

3. Start the API server:

```powershell
python scripts/run_api.py
```

4. Open the UI:

- `http://127.0.0.1:8000`

5. Call the diagnosis API:

```powershell
curl -X POST "http://127.0.0.1:8000/api/v1/diagnosis/run" `
  -F "problem_name=strawberry diagnosis" `
  -F "case_text=leaf spots and mildew" `
  -F "stage=initial" `
  -F "n_rounds=2" `
  -F "image=@asserts/output.png"
```
