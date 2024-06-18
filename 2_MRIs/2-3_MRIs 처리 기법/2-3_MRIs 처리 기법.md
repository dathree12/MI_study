# 3. MRIs 처리 기법

Modality: MRI
Created by: 태훈 이

# **Python으로 dicom 파일 다루기**

데이터처리/인공지능 분야에서 주로 사용되는 프로그래밍 언어인 'python'에서 dicom 파일을 읽어오고, 전처리 하는 방법에 대해서 살펴보자.

## **Dicom image 및 header 읽어오기**

Dicom 파일을 읽어오기 위해서는 pydicom 패키지를 사용하면 된다.

아래와 같이 image 정보 및 헤더를 읽어올 수 있다.

(헤더는 헤더이름을 띄어쓰기 없이 Camel case로 작성하여 읽어오면 된다.)

```python
import pydicom

ds = pydicom.read_file(_file)

pixel_array = ds.pixel_array# dicom image
Rescale_slope = ds.RescaleSlope# dicom header (Rescale slope)
Rescale_intercept = ds.RescaleIntercept# dicom header (Rescale intercept)
Window_center = ds.WindowCenter# dicom header (Window center)
Window_width = ds.WindowWidth# dicom header (Window width)
Photometric_interpretation = ds.PhotometricInterpretation# dicom header (Photometric interpretation)
```

## **Dicom image 띄우기**

파일을 읽어온 이후에 이미지를 확인하기 위해서는 읽어온 파일을 plot하는 것이 아닌 파일의 pixel_array attribute를 plot해주면 된다.

```python
plt.imshow(ds.pixel_array, cmap='gray')
```

![https://blog.kakaocdn.net/dn/bgGBU9/btrb2aKi31T/bReL31kOnJVN86AWyRkC50/img.png](https://blog.kakaocdn.net/dn/bgGBU9/btrb2aKi31T/bReL31kOnJVN86AWyRkC50/img.png)

## **헤더정보를 활용하여 Dicom image 전처리하기**

### 1. Dicom image pixel의 range를 bits stored 정보를 활용하여 normalization하기

```python
ds = pydicom.read_file(file)
img = ds.pixel_array.astype(np.float32)
img = (img / (2 ** ds.BitsStored))
```

### 2. Dicom stored pixel 값을 실제 image 값으로 변환하기

```python
if(('RescaleSlope'in ds)and ('RescaleIntercept'in ds)):
    pixel_array = (pixel_array * ds.RescaleSlope) + ds.RescaleIntercept
```

### 3. 보고자 하는 조직에 맞게 영상 강조하기

```python
if('WindowCenter'in ds):
	if(type(ds.WindowCenter) == pydicom.multival.MultiValue):
        window_center = float(ds.WindowCenter[0])
        window_width = float(ds.WindowWidth[0])
        lwin = window_center - (window_width / 2.0)
        rwin = window_center + (window_width / 2.0)
	else:
        window_center = float(ds.WindowCenter)
        window_width = float(ds.WindowWidth)
        lwin = window_center - (window_width / 2.0)
        rwin = window_center + (window_width / 2.0)
else:
	lwin = np.min(pixel_array)
	rwin = np.max(pixel_array)

pixel_array[np.where(pixel_array < lwin)] = lwin
pixel_array[np.where(pixel_array > rwin)] = rwin
pixel_array = pixel_array - lwin
```

### 4. Viewer에서 보이는대로 image 변경하기

```python
if(ds.PhotometricInterpretation == 'MONOCHROME1'):
    pixel_array[np.where(pixel_array < lwin)] = lwin
    pixel_array[np.where(pixel_array > rwin)] = rwin
    pixel_array = pixel_array - lwin
    pixel_array = 1.0 - pixel_array

else:
    pixel_array[np.where(pixel_array < lwin)] = lwin
    pixel_array[np.where(pixel_array > rwin)] = rwin
    pixel_array = pixel_array - lwin
```

# **Python으로 NIfTI 파일 다루기**

## **1. Nibabel 설치하기**

Nibabel은 pip를 사용하여 쉽게 설치할 수 있습니다. 터미널이나 커맨드 프롬프트에서 다음 명령어를 실행하세요:

```bash
pip install nibabel
```

## **2. NIfTI 파일 불러오기**

Nibabel을 사용하여 NIfTI 파일을 불러오고, 파일 내의 데이터와 메타데이터를 접근하는 기본적인 방법을 보여주는 코드입니다.

```python
import nibabel as nib

# NIfTI 파일 경로
file_path = 'your_file_path_here.nii'

# NIfTI 파일 불러오기
nifti_file = nib.load(file_path)

# 영상 데이터 접근하기
image_data = nifti_file.get_fdata()

# 헤더 정보 접근하기
header = nifti_file.header

print("영상 데이터의 차원:", image_data.shape)
print("datatype:", header['datatype'])
print("bitpix:", header['bitpix'])
```

## **3. 영상 데이터 전처리하기**

상단에서 언급한 항목들을 고려하여 간단한 전처리 예제를 제공합니다. 여기서는 영상 데이터의 차원 축소, 데이터 타입 변환, 스케일링 등을 수행합니다.

```python
import numpy as np

# 차원 축소 예제 (4D -> 3D)
if image_data.ndim == 4:
    image_data = image_data[:,:,:,0]

# 데이터 타입 변환 예제 (float32로 변환)
image_data = image_data.astype(np.float32)

# 스케일링 (nan 값이 있을 경우 처리)
if np.isnan(header['scl_slope']) or np.isnan(header['scl_inter']):
    print("scl_slope 또는 scl_inter가 정의되지 않았습니다.")
else:
    image_data = image_data * header['scl_slope'] + header['scl_inter']

# 결과 확인
print("전처리 후 영상 데이터의 차원:", image_data.shape)
print("전처리 후 데이터 타입:", image_data.dtype)
```