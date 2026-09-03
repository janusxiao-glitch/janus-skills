---
name: chibi-realistic-miniature-character
description: Create or apply a reusable image-editing prompt that turns a supplied person photo into a photorealistic chibi or miniature-body character while preserving identity, face detail, clothing, props, pose, expression, background, scene, composition, and lighting. Use when the user asks for 小人, Chibi, 大头小身, realistic miniature person, body-proportion restyling, or a reusable prompt/Skill for this effect. Do not use for cartoon or anime illustration, generic portrait retouching, background replacement, outfit replacement, or images without a person reference.
---

# 写实“小人”人物重绘

把一张人物照片整理成可执行的图像编辑提示词：只改变人物的身体比例和必要的画面占比，保留原照片的身份、动作、服装、道具、环境和摄影质感。

## 核心约束

- 将原图作为唯一视觉参考；除非用户明确要求，否则不改变背景、场景布局、机位、构图、服装、配饰、手持道具、动作方向、表情或全局光影。
- 采用夸张但写实的“大头小身子”比例。头部只轻微放大，`head_scale` 不得超过 1.5；躯干、手臂和腿部明显缩小，但仍要符合原动作和道具交互关系。
- 绝不重绘或美化人脸。保持五官、脸型、肤色、发型、发丝细节、年龄特征和可识别身份一致；脸部清晰度优先于比例夸张度。
- 保持照片级真实感：真实皮肤、布料、材质、景深、接触阴影和光线方向。缩小后的身体必须像真实存在于现场的微缩人物，而不是卡通、插画、玩偶或 3D 渲染。
- 默认保持人物在画面中的位置与整体视觉占比（`subject_frame_scale=1.0`）。只有用户明确要求时，才改变人物整体在画面中的大小；改变时也不得裁切、移动或重构背景。
- 任何因身体缩小而暴露的背景区域，都要依据原场景无痕补全，不能出现重复纹理、拼接边缘、透明空洞或不合逻辑的遮挡。

## Workflow

1. 检查是否有可用的人物照片。若没有图像参考，只能返回模板并说明需要附图，不能声称已完成重绘。
2. 从原图提取并锁定：人物身份与脸部特征、服装款式和颜色、道具、动作与姿势、表情、背景、构图、镜头视角、光影和景深。
3. 使用参数默认值生成正向提示词和禁止项；除非用户指定，否则采用 `head_scale=1.25`、`torso_scale=0.45`、`arm_scale=0.35`、`leg_scale=0.35`、`subject_frame_scale=1.0`。
4. 阅读并填写 `references/prompt-template.md`。将用户指定内容放入参数字段；未指定字段使用默认值，不凭空添加场景、服装或道具。
5. 使用当前可用的图像编辑工具，把原图作为参考输入执行一次重绘；工具不可用时，输出完整可复制提示词和参数，不要假装已经生成图片。
6. 用以下验收清单检查结果：
   - 面部身份、五官和表情可辨且没有卡通化；
   - 头部放大不超过 1.5 倍，身体各部位确实缩小；
   - 服装、颜色、纹理、道具和动作关系保持不变；
   - 背景、构图、人物位置和光影没有无关变化；
   - 身体与地面、道具和环境的接触阴影自然，没有拼接、拉伸、重影或额外肢体；
   - 输出仍是照片级写实效果。
7. 交付时说明所用参数，并在需要时同时给出正向提示词、禁止项和简短验收结果。

## 参数字段

| 字段 | 默认值 | 约束 |
| --- | --- | --- |
| `head_scale` | `1.25` | 仅轻微放大，最大 `1.5` |
| `torso_scale` | `0.45` | 相对原图缩小，保持服装结构可辨 |
| `arm_scale` | `0.35` | 缩小但保留原动作、关节方向和道具接触 |
| `leg_scale` | `0.35` | 缩小但保留站姿、步态和重心逻辑 |
| `subject_frame_scale` | `1.0` | 默认不改变人物在画面中的整体占比 |
| `face_identity_lock` | `strict` | 不得改脸、换脸、美颜或年龄化 |
| `scene_lock` | `strict` | 不得换背景、换场景、改机位或改构图 |
| `realism` | `photographic` | 禁止卡通、插画、玩偶和 CGI 观感 |

## 禁止项

禁止把任务改写成卡通头像、动漫、绘本、3D 玩偶、黏土模型、Q 版插画或夸张美颜；禁止换脸、改变发型或年龄、增删肢体、改变服装和道具、改动动作或表情、替换背景、改变天气和时间、添加文字/水印/边框、改变相机视角、制造漂浮感、出现断肢重影或不自然接触阴影。

## 资源路由

- 需要生成或编辑提示词时，阅读 `references/prompt-template.md` 并完整填写其中的字段。
- 不需要脚本、外部数据、联网资源或额外素材；这是一个 instruction-only Skill。

## Validation

在修改 Skill 文件后运行：

```bash
python3 C:/Users/janus/.codex/skills/standard-skill-maker/scripts/validate_skill.py <skill-folder>
```

至少用一个包含“人物照片 + 小人/Chibi + 保持背景和脸部不变”的请求测试触发，用一个“只做背景替换”的请求测试不应触发。

