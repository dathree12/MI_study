# 3. Ultrasonography 데이터 처리 기법

Modality: Ultrasonography
Created by: 세연 박

### DICOM 데이터 읽기

- Pydicom, SimpleITK 모듈을 사용

```python
import pydicom

# 데이터 읽기
filename = './0001.dcm'
dcm = pydicom.dcmread(filename)

# 특정 attribute에 접근하면 관련 데이터 값 획득
# attribute -> PateintSex, pixel_array
sex = dcm.PateintSex 
img = dcm.pixel_array
```

```python
import SimpleITK as sitk

# 데이터 읽기
filename = './0001.dcm'
images = sitk.ReadImage(filename)

image_array = sitk.GetArrayFromImage(images)
```

### 전처리 방법

- Filtering the image and removing the noise by various filtering techniques
    - Salt-and-pepper and Gaussian noise
    - Mean, median and Wiener filters
- Augmentation
    - color jitter (brightness, contrast)
    - Rotate, flip

- Shadow detection, removal
    - 초음파 이미지에서 shadow는 구조물의 디테일을 잃어버려 정확한 진단에 방해가 됨
    - Shadowing image를 처리하는 여러 선행 연구 존재
        - Shadow-consistent Semi-supervised Learning for Prostate Ultrasound Segmentation
        - Unsupervised Learning for Acoustic Shadowing Artifact Removal in Ultrasound Imaging
        - Deep Semi-Supervised Ultrasound Image Segmentation by Using a Shadow Aware Network With Boundary Refinement
        - Automated Placenta Segmentation with a Convolutional Neural Network Weighted by Acoustic Shadow Detection
        - Acoustic Shadow Detection: Study and Statistics of B-Mode and Radiofrequency Data

### References

- [https://link.springer.com/chapter/10.1007/978-3-030-00665-5_159](https://link.springer.com/chapter/10.1007/978-3-030-00665-5_159)
- [https://github.com/pydicom/pydicom](https://github.com/pydicom/pydicom)
- [https://89douner.tistory.com/284](https://89douner.tistory.com/284)