# ComfyUI Studio

ComfyUI AI 创作工作室插件。通过 Comfy Pilot MCP 与 ComfyUI 通信，提供图像生成、视频制作、语音合成、LoRA 训练等全流程能力。

## 全局规则

1. 所有 ComfyUI 相关操作前，先检查 .comfyui-studio/inventory.json 是否存在。
   不存在则提示用户运行 /studio-scan。

2. 读取 inventory.json 中的 recommended_tier 字段进行硬件适配：
   - low_vram (≤12GB): 优先推荐 GGUF 量化、FramePack、AnimateDiff、tiled VAE
   - mid_vram (12-20GB): 可用 FLUX FP16、Wan 2.1 量化版
   - high_vram (≥24GB): 全功能，Wan 2.2 14B、FLUX 原生

3. 所有输出使用中文。

4. 构建工作流时，必须交叉校验 inventory.json 中的已安装模型和节点。
   缺失的模型/节点应在执行前提示用户安装。

5. 工作流 JSON 必须使用 ComfyUI API 格式（非 UI 格式）。

## Comfy Pilot MCP 工具参考

插件通过 Comfy Pilot MCP 与 ComfyUI 通信，可用工具包括：
- get_workflow — 获取当前工作流
- edit_graph — 编辑图形节点（创建/删除/移动/连接）
- run — 执行工作流
- view_image — 查看输出图片
- download_model — 下载模型（支持 HuggingFace、CivitAI）

注意：以上工具名称需在安装 Comfy Pilot 后验证实际名称。

## inventory.json 标准 Schema

所有 Skill/Agent/Command 引用 inventory.json 时，必须使用以下字段路径：

```json
{
  "hardware": {
    "gpu_name": "RTX 4060",
    "gpu_vram_gb": 8,
    "gpu_vram_free_mb": 6000,
    "ram_gb": 32,
    "recommended_tier": "low_vram"
  },
  "installed_models": {
    "checkpoints": [{"name": "model.safetensors", "size_gb": 6.5}],
    "loras": [],
    "controlnet": [],
    "vae": [],
    "clip": [],
    "unet": [],
    "embeddings": [],
    "upscale_models": [],
    "ipadapter": [],
    "clip_vision": []
  },
  "installed_nodes": ["ComfyUI-WanVideoWrapper", "ComfyUI-GGUF"],
  "comfyui_path": "D:/ComfyUI-aki-v3/ComfyUI",
  "comfy_pilot_installed": true,
  "tokens": {
    "hf_token": true,
    "civitai_token": true
  },
  "scan_date": "2026-03-12"
}
```

- 显存档位在 `hardware.recommended_tier`
- 已安装模型在 `installed_models.*`
- 已安装节点在 `installed_nodes`
- FLUX/SDXL 的 prompt 仍需使用英文（模型训练数据以英文为主），中文 prompt 可能导致质量下降
