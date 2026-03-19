---
title: "기초적인 서피스 쉐이더를 제작해 봅시다"
date: 2026-03-19 10:00:00 +0900
categories: [Unity, Shader]
tags: [unity, shader]
---

## Standard Surface Shader

```
Shader "Custom/Sphere"
{
    Properties
    {
        _Color ("Color", Color) = (1,1,1,1)
        _MainTex ("Albedo (RGB)", 2D) = "white" {}
        _Glossiness ("Smoothness", Range(0,1)) = 0.5
        _Metallic ("Metallic", Range(0,1)) = 0.0
    }
    SubShader
    {
        Tags { "RenderType"="Opaque" }
        LOD 200

        CGPROGRAM
        #pragma surface surf Standard fullforwardshadows

        #pragma target 3.0
        sampler2D _MainTex;

        struct Input
        {
            float2 uv_MainTex;
        };

        half _Glossiness;
        half _Metallic;
        fixed4 _Color;

        UNITY_INSTANCING_BUFFER_START(Props)            
        UNITY_INSTANCING_BUFFER_END(Props)

        void surf (Input IN, inout SurfaceOutputStandard o)
        {            
            fixed4 c = tex2D (_MainTex, IN.uv_MainTex) * _Color;
            o.Albedo = c.rgb;         
            o.Metallic = _Metallic;
            o.Smoothness = _Glossiness;
            o.Alpha = c.a;
        }
        ENDCG
    }
    FallBack "Diffuse"
}

```

---

``` 
Shader "Custom/Sphere" 
```

쉐이더 이름

---

```
Properties
{
    _Color ("Color", Color) = (1,1,1,1)
    _MainTex ("Albedo (RGB)", 2D) = "white" {}
    _Glossiness ("Smoothness", Range(0,1)) = 0.5
    _Metallic ("Metallic", Range(0,1)) = 0.0
}
```
마테리얼 인스펙터에 노출되는 값

* 이름 : _Color
* 인스펙터 표시 이름 : Color
* 타입 : Color
* 기본값 : (1,1,1,1)

---

```
SubShader
{
    ...
}
```

실제로 GPU 가 사용할 쉐이더 정의가 들어가는 곳

---

```
Tags { "RenderType"="Opaque" } 
```

Tags
* 유니티에게 이 쉐이더를 어떻게 분류할지 알려주는 메타데이터
* 렌더링 파이프라인에서 참고하는 설정값
  
RenderType
* 이 쉐이더가 어떤 종류의 물체인지 분류하는 용도

RenderType 종류
* Opaque - 불투명 오브젝트
* Transparent - 투명 오브젝트
* TransparentCutout - 알파 컷아웃
* Background - 배경
* Overlay - 오버레이

---

