# Shader v2f 结构体变量引用错误

## 问题描述

在 `Add_CenterGlow_Ultra.shader` 中发现一个变量引用错误：

```hlsl
half4 frag(v2f i) : SV_Target
{
    // ...
    float2 maskUV = float2(i.uv.z, i.texcoord.y * _Mask_ST.y + _Mask_ST.w);
    //                              ^^^^^^^^^^^^ 错误!
}
```

## 错误原因

在片元着色器中引用了 `i.texcoord`，但 `v2f` 结构体中并没有定义 `texcoord` 成员：

```hlsl
struct v2f
{
    float4 vertex : SV_POSITION;
    half4 color : COLOR;
    float4 uv : TEXCOORD0;  // 只有 uv，没有 texcoord
    UNITY_FOG_COORDS(1)
};
```

## 解决方案

### 方案1：使用已有的 uv 数据

如果 Mask 是径向渐变（中心到边缘），可以简化为 1D 查找：

```hlsl
half mask = tex2D(_Mask, float2(i.uv.z, i.uv.z)).r;
```

### 方案2：在顶点着色器中正确传递 Mask UV

修改 v2f 结构体和顶点着色器：

```hlsl
struct v2f
{
    float4 vertex : SV_POSITION;
    half4 color : COLOR;
    float4 uv : TEXCOORD0;     // xy=MainTex, zw=Mask
    float customData : TEXCOORD1;
    UNITY_FOG_COORDS(2)
};

v2f vert(appdata v)
{
    // ...
    o.uv.zw = v.texcoord.xy * _Mask_ST.xy + _Mask_ST.zw; // 完整 Mask UV
    o.customData = v.texcoord.z;
    // ...
}

half4 frag(v2f i) : SV_Target
{
    half mask = tex2D(_Mask, i.uv.zw).r;
    half glow = saturate(mask - (1.0 - i.customData));
    // ...
}
```

## 教训

1. **v2f 结构体是顶点到片元的唯一数据通道** - 片元着色器只能访问 v2f 中定义的成员
2. **appdata 中的数据在片元着色器中不可直接访问** - 必须通过顶点着色器传递到 v2f
3. **编译器可能不会报错** - 某些情况下会静默失败或产生未定义行为

## 相关文件

- `Shaders/ASE/CenterGlow/Add_CenterGlow_Ultra.shader`

## 标签

`Unity` `Shader` `Bug` `v2f` `片元着色器`

---
创建时间: 2025-12-23
