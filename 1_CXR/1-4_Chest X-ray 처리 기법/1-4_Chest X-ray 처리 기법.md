# 4. Chest X-ray 처리 기법

Modality: CXR
Created by: 건희 이

# Libraries

- **pydicom**
- SimpleITK

# pydicom

### Install

- `conda install -c conda-forge pydicom`
- `pip install pydicom`

### Read data

```python
import pydicom

dcm = pydicom.read_file(file_path)

# CXR image
img = dcm.pixel_array # 

# CXR 주요 tag
bs = dcm.BitsStored
md = dcm.Modality
sd = dcm.StudyDescription
pi = dcm.PhotometricInterpretation

# 태훈쌤 4.MRIs 처리 기법 참고
```

### 주요 tag

- Bits Stored
    - Dicom 파일의 이미지 정보가 저장되어 있는 비트(bit)의 수
    - 주로 10~16 bit
- Modality
    - CT / CR / MR 등
- StudyDescription
    - AP / PA / Lateral / AI 등
- Photometric Interpretation
    - MONOCHROME1: 최소값을 갖는 pixel이 하얀색으로 나타나는 gray scale image.
    - MONOCHROME2: 최소값을 갖는 pixel이 검정색으로 나타나는 gray scale image.
    - RGB: color image
    
    <img src="/1_CXR/1-4_Chest X-ray 처리 기법/Untitled.png"></img><br/>
    
    좌: MONOCHROME2, 우: MONOCHROME1
    

# 보편적인 CXR 전처리

- Resize
- Normalization
- Histogram Equalization
