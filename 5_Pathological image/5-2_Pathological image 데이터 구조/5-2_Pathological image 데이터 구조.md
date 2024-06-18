# 2. Pathological image 데이터 구조

Modality: Pathological image
Created by: Daseul Park

# 1. 병리 이미지 데이터 구조 (매우 중요)

## **1.1 Magnification**

- Magnification(확대율): 확대율은 이미지를 실제 크기보다 얼마나 크게 표시하는지를 나타내는 비율입니다. 높은 확대율은 더 작은 객체들을 더 크게 보여주기 때문에 세밀한 구조를 확인하는 데 도움이 됩니다. 일반적으로, 병리 이미지의 확대율은 고해상도 이미지를 더 크게 표시하는 데 사용됩니다.
- 40x objective has a 400x total magnification

## **1.2 Level**

| Level | Resolution |
| --- | --- |
| 0 | Full resolution |
| 1 | ½ |
| 2 | ½^2 |
| 3 | ½^3 |
| 4 | ½^4 |

## **1.3  MPP**

- MPP(Microns per Pixel, 픽셀 당 마이크론): MPP는 이미지의 해상도와 확대율을 결합한 개념으로, 픽셀 하나당 몇 마이크론의 실제 길이를 나타냅니다. MPP는 실제 세계에서 이미지 내의 픽셀과의 대응을 제공합니다. 예를 들어, MPP가 0.5라면 픽셀 하나는 실제 세계에서 0.5 마이크론을 의미합니다.
    
    
    | MPP | 스캔 |
    | --- | --- |
    | 1 | 100x |
    | 0.5 | 200x |
    | 0.25 | 400x |

<img src="/5_Pathological image/5-2_Pathological image 데이터 구조/Untitled.png"></img><br/>

<img src="/5_Pathological image/5-2_Pathological image 데이터 구조/Untitled 1.png"></img><br/>

**💛병리 이미지 분석에는 배율이 매우 중요하여 MPP/배율을 연구 자료에 꼭 삽입 하는 것을 추천**

# 2. 스캐너 종류에 따른 데이터 형식


<img src="/5_Pathological image/5-2_Pathological image 데이터 구조/Untitled 2.png"></img><br/>
이미지 출처 : [https://aggc22.grand-challenge.org/Data/](https://aggc22.grand-challenge.org/Data/)

- Akoya Biosciences, Olympus, Zeiss, Leica, KFBio, Philips scanner, 사진에는 없지만 Hamamatsu가 존재
- [https://www.drivendata.org/competitions/67/competition-cervical-biopsy/page/256/](https://www.drivendata.org/competitions/67/competition-cervical-biopsy/page/256/)
- **서울대학교병원은 Leica에서 제조한 스캐너 사용**

## 2.1 Leica biosystem scanner (서울대학교병원 사용)

### 2.1.1 데이터 형식

- SVS 형식의 파일 생성

### 2.1.2 viewer & labeling tool

- Aperio image scope viewer 다운로드 링크

[Aperio ImageScope - Pathology Slide Viewing Software](https://www.leicabiosystems.com/digital-pathology/manage/aperio-imagescope/)

- svs 파일만 레이블링 가능

## 2.2 그 외 (ZEISS, Hamamatsu, KFBio 등등)

### 2.2.1 데이터 형식

- ZEISS, Hammatsu 는 mrxs 형식의 파일 과 dat파일 한 세트로 생성
- 또는 Tiff 파일의 형식으로 오는 경우가 많음

### 2.2.2 viewer & labeling tool

- Automated Slide Analysis Platform (ASAP) [https://computationalpathologygroup.github.io/ASAP/](https://computationalpathologygroup.github.io/ASAP/)

# 3. Reference

디지털 병리 관련 읽어보면 좋은 기사 ([http://www.kmdianews.com/news/articleView.html?idxno=60762](http://www.kmdianews.com/news/articleView.html?idxno=60762))

- 디지털 병리 가이드라인 권고안 (jpathololtm)
 
[https://biology-statistics-programming.tistory.com/211](https://biology-statistics-programming.tistory.com/211)

```
print(f'level count          :: {slide.level_count}')
print(f'image size per level :: {slide.level_dimensions}')
print(f'level 0 MPP          :: {slide.properties["openslide.mpp-x"]}')
```

출처:

[https://biology-statistics-programming.tistory.com/215](https://biology-statistics-programming.tistory.com/215)

[히비스서커스의 블로그:티스토리]