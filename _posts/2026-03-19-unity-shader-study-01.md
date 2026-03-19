---
title: "기초적인 서피스 쉐이더를 제작해 봅시다"
date: 2026-03-19 10:00:00 +0900
categories: [Unity, Shader]
tags: [unity, shader]
---

# Standard Surface Shader

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

## 쉐이더 이름

``` 
Shader "Custom/Sphere" 
```

---

## Properties

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

## SubShader

```
SubShader
{
    ...
}
```

실제로 GPU 가 사용할 쉐이더 정의가 들어가는 곳

---

## Tags

```
Tags { "RenderType"="Opaque" } 
```

유니티에게 이 쉐이더를 어떻게 분류할지 알려주는 메타데이터

렌더링 파이프라인에서 참고하는 설정값
  
* RenderType
  * 이 쉐이더가 어떤 종류의 물체인지 분류하는 용도
  * RenderType 종류
    * Opaque - 불투명 오브젝트
    * Transparent - 투명 오브젝트
    * TransparentCutout - 알파 컷아웃
    * Background - 배경
    * Overlay - 오버레이

---

## RenderType 비교

| 방식              | 보이는 방식         | 반투명 가능? | 픽셀 버리기 |  정렬 문제 | 대표 예시       |
| --------------- | -------------- | ------: | -----: | -----: | ----------- |
| **Opaque**      | 완전히 불투명        |      불가 |     없음 |  거의 없음 | 벽, 돌, 금속    |
| **Cutout**      | 보이거나 아예 안 보이거나 |      불가 |     있음 | 비교적 적음 | 나뭇잎, 철망, 잔디 |
| **Transparent** | 비쳐 보임          |      가능 |  보통 없음 |     많음 | 유리, 물, 연기   |

---

## LOD

* 쉐이더의 품질/복잡도 레벨을 나타내는 숫자
* 메쉬의 LOD 와는 다른 개념
* 200은 보통 중간 정도의 일반적인 쉐이더 수준으로 많이 쓰임

---

## CGPROGRAM

쉐이더 코드가 들어있는 블록

---

## #pragma

Surface Shader 선언문

```
#pragma surface surf Standard fullforwardshadows
```

* surface - SurfaceShader 를 의미
* surf - 함수 이름
* Standard - 사용할 라이팅 모델 이름
* fullforwardshadows - forward 렌더링에서 그림자 처리를 더 완전하게 포함하도록 요청

---

## #pragma target 3.0

쉐이더 모델 3.0 이상을 요구함

---

## 선언

```
sampler2D _MainTex
```

_MainTex 라는 2D 텍스처를 선언

```
half _Glossiness;
half _Metallic;
fixed4 _Color;
```

Properties 에 있던 값들을, 코드에서 사용할 수 있도록 선언

---

## 인덱싱 버퍼

```
UNITY_INSTANCING_BUFFER_START(Props)

...
   
UNITY_INSTANCING_BUFFER_END(Props)
```

GPU Instancing

---

## 함수

```
void surf (Input IN, inout SurfaceOutputStandard o)
{
    fixed4 c = tex2D (_MainTex, IN.uv_MainTex) * _Color;
    o.Albedo = c.rgb;
    o.Metallic = _Metallic;
    o.Smoothness = _Glossiness;
    o.Alpha = c.a;
}

```

* IN - 입력값
* o - 출력값

---

```
fixed4 c = tex2D (_MainTex, IN.uv_MainTex) * _Color;
```

_MainTex 를 UV 좌표로 샘플링하여 색을 가져옴. 거기에 _Color 값을 곱함

---

```
o.Albedo = c.rgb;
```

물체의 기본 색상

---

```
o.Metallic = _Metallic;
```

금속성

---

```
o.Smoothness = _Glossiness;
```

평활도

---

```
o.Alpha = c.a;
```

알파값 설정

---

## Fallback

```
FallBack "Diffuse"
```

만약 이 쉐이더를 현재 환경에서 사용할 수 없으면, 더 단순한 Diffuse 쉐이더로 대체하라는 의미
