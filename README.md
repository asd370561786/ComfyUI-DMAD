# ComfyUI-DMAD

Custom nodes that run the DMAD 4-step students of MiniMax-H3 in ComfyUI the way they were trained. ComfyUI's stock
`lcm` sampler with the `simple` scheduler (after `ModelSamplingMiniMaxH3`) implements the same rule and sigma grid and
gives bit-identical videos, so these nodes are a convenience; either works.

* **DMAD Sampler (re-noise)**: a `SAMPLER` for `SamplerCustom` / `SamplerCustomAdvanced`. At every step the model
  predicts the clean sample x0 and the next input is x0 re-noised with fresh noise at the next sigma
  (x' = (1 - sigma') x0 + sigma' eps), the DMD-style multistep rule the students were trained with (the same update as `lcm`).
  ODE samplers (euler, ...) step from x_t instead, which is not the students' operating point.
* **DMAD Sigmas**: the students' sigma grid, shifted linear from 1 to 0, shift 12 (video), `steps` model evaluations.

The ComfyUI-layout LoRAs, `minimax_h3/dmad_minimax_h3_4step_{lora_critic,full_critic}_comfyui.safetensors` on the
Hugging Face repo, are exact conversions of the released LoRAs.

## Install

Copy or symlink this folder into `ComfyUI/custom_nodes/`.

The base model, text encoder and VAEs are the Comfy-Org MiniMax-H3 repackage.

## Settings that matter

- LoRA strength: `1.0`
- cfg: `1.0`
- `shift_audio`: `2.0` in `ModelSamplingMiniMaxH3`
- Steps: `4` with the DMAD Sigmas node

The students are trained in continuous time, so the same sampler also supports more steps. On prompts with fast motion,
8 and 12 steps render fast movements cleaner than 4-step.

Upstream repository: https://github.com/Yzmblog/DMAD
