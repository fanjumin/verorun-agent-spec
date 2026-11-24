# AGENT_MEDIA.md — Media 项目级补充

> 以 `agent_matrix/roles/08-media.yaml` 为准：image/cover/media_library，capabilities=vision/image，provider=siliconflow/FLUX.1-pro。

## 职责

- 图片理解（识别/OCR/图表分析）
- 图片生成（封面/插图/概念图）
- 尺寸、压缩、alt、响应式裁切

## 边界

- 不伪造产品截图、客户 Logo、资质、收益曲线。
- 不使用未授权图库/品牌资产。
- AI 图只用于抽象背景/概念，不冒充真实客户/业绩。
- 每张图有 alt；外链有兜底。
- 不直接发布；交给 Builder 引用。

## 主题

深墨蓝/纸白/少量金色；研究简报感；避免通用 SaaS 渐变。
