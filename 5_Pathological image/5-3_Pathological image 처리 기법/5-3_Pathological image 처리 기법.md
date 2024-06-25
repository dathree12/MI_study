# 3. Pathological image 처리 기법

Modality: Pathological image
Created by: Daseul Park

# 1. 데이터 불러오기

Open slide library 사용 **(svs, mrxs, tif 모두 사용 가능)**

pip install openslide-python 사용하여 설치                                                                                

```jsx

import openslide

```

# 2. 데이터 기본 정보 확인

슬라이드 이미지의 기본 정보 확인은 아래 코드와 같습니다.

```jsx
import numpy as np
from openslide import OpenSlide
import matplotlib.pyplot as plt

svs_file_path ='practice_img.svs'

#open svs file
slide = OpenSlide(svs_file_path)

#print information
mpp = round(float(slide.properties['aperio.MPP']),2)
dimension = slide.dimensions
level_count = slide.level_count
level_dimension = slide.level_dimensions

print('===== whole slide image information =====')
print(f'mpp             : {mpp}')
print(f'dimension       : {dimension}')
print(f'level_count     : {level_count}')
print(f'level_dimension : {level_dimension}')

# slide 예시 이미지 링크 
# https://portal.gdc.cancer.gov/analysis_page?app=Downloads
# **HCM-CSHL-0091-C25-01A-01-S2-HE.7BBCD78A-9076-40CD-89DF-7052CF9BF6BC.SVS 예시 이미지**
```

현재 아래 예시 이미지에서

!<img src="/5_Pathological image/5-3_Pathological image 처리 기법/Untitled.png"></img><br/>

- level은 0,1,2,3로 총 4가지 이고
이미지 크기는 각각  (81672, 18557), (20418, 4639), (5104, 1159), (2552, 579) 인 것을 확인 할 수 있습니다.
- 또한 level 0 에서의 mpp 0.25로 400배율이고
level 1일 경우는 level 0의 경우보다 4배 작으므로 100배
level 2일 경우는 level 1의 경우보다 4배 작으므로 25배
level 3일 경우는 level 2의 경우보다 2배 작으므로 12.5배 임을 확인 할 수 있습니다.
- 이러한 정보를 확인 하는 이유는 병리 이미지의 크기가 매우 크기 때문에
패치 단위로 나누어 딥러닝 모델 학습에 이용하기 때문입니다.
- **배율을 확인하여 학습에 유리한 패치를 추출하면 됩니다.**

```python
#check slide image 
level_idx = 3
wsi_w,wsi_h = slide.level_dimensions[level_idx]
wsi_array = np.array(slide.read_region([0,0], level_idx, [wsi_w, wsi_h]))[:,:,:3]

plt.imshow(wsi_array)
```

!<img src="/5_Pathological image/5-3_Pathological image 처리 기법/Untitled 1.png"></img><br/>

- 이미지 확인은 위 코드로 하면 되며, 원하는 level의 이미지의 index를 넣어 추출하면 됩니다. 예시 코드는 이미지 크기로 인해 level3의 이미지로 보여주기 실행하였습니다.