# Unity 粒子特效 Shader 移动端优化指南

## 背景

在 Unity 项目中对 CenterGlow 系列粒子 Shader 进行移动端性能优化，从完整版逐步优化到极致版。

## 问题

原始 Shader 在移动端性能较差：
- 4 次纹理采样
- 3 次依赖纹理读取 (dependent texture read)
- 运行时分支判断
- 使用 float 精度

## 优化方案

### 1. 移除依赖纹理读取

**问题**: Flow 扭曲导致 MainTex 的 UV 依赖于 Flow 贴图采样结果

**解决**: 移除 Flow 扭曲，UV 在顶点着色器预计算

```hlsl
// 优化前 (片元着色器)
float2 distortedUV = mainUV - tex2D(_Flow, flowUV).rg * power;
float4 mainTex = tex2D(_MainTex, distortedUV); // 依赖读取!

// 优化后 (顶点着色器预计算)
o.uv01.xy = mainUV + _Speed.xy * _Time.y;
// 片元着色器直接使用
float4 mainTex = tex2D(_MainTex, i.uv01.xy); // 无依赖
```

### 2. 编译时分支替代运行时分支

**问题**: `if (_Toggle > 0.5)` 在 GPU 上有分支预测开销

**解决**: 使用 `shader_feature` 生成变体

```hlsl
// 优化前
if (_Usecenterglow > 0.5) {
    // 中心发光代码
}

// 优化后
#pragma shader_feature_local _CENTERGLOW_ON

#if defined(_CENTERGLOW_ON)
    // 中心发光代码 - 编译时决定，无运行时开销
#endif
```

### 3. 使用 half 精度

**问题**: float (32bit) 在移动端带宽消耗大

**解决**: 颜色/UV 等使用 half (16bit)

```hlsl
// 优化前
float4 color;
float2 uv;

// 优化后
half4 color;
half2 uv;
```

### 4. 合并 Alpha 计算

**问题**: Alpha 分散在多处乘法

**解决**: 预乘合并减少指令数

```hlsl
// 优化前
col.rgb = mainTex.rgb * noise.rgb * _Color.rgb * i.color.rgb;
col.rgb *= mainTex.a;
col.rgb *= noise.a;
col.rgb *= _Color.a;
col.rgb *= i.color.a;

// 优化后
half combinedAlpha = mainTex.a * noise.a * _Color.a * i.color.a;
half3 col = mainTex.rgb * noise.rgb * _Color.rgb * i.color.rgb * combinedAlpha;
```

## 性能对比

| 版本 | 纹理采样 | ALU 指令 | 依赖读取 | 适用设备 |
|------|----------|----------|----------|----------|
| 完整版 | 4次 | ~50条 | 3次 | PC/高端 |
| Lite版 | 3次 | ~20条 | 0次 | 中端移动 |
| Optimized版 | 2-3次 | ~15条 | 0次 | 中低端移动 |
| Ultra版 | 2次 | ~10条 | 0次 | 极低端 |

## 关键优化原则

1. **避免依赖纹理读取** - 移动 GPU 的最大性能杀手
2. **UV 计算移至顶点着色器** - 片元数量远大于顶点
3. **使用 shader_feature 而非运行时分支** - 编译时决定代码路径
4. **使用 half 精度** - 移动端带宽敏感
5. **合并贴图通道** - 减少采样次数

## 文件位置

```
Shaders/ASE/CenterGlow/
├── Add_CenterGlow.shader              (完整版)
├── Add_CenterGlow_Lite.shader         (移动端优化版)
├── Add_CenterGlow_Lite_Optimized.shader (进阶优化版) ★推荐
├── Add_CenterGlow_Ultra.shader        (极致优化版)
└── README.txt                         (详细说明)
```

## 标签

`Unity` `Shader` `移动端优化` `粒子特效` `性能优化`

---
创建时间: 2025-12-23
项目: VFX_Effect
