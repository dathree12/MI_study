# 2. MRIs 데이터 구조

Modality: MRI
Created by: 태훈 이

# MRI Data 구조 (DICOM, NIfTI)

DICOM : [**DICOM(Digital Imaging and Communications in Medicine)**](http://www.dicomstandard.org/)은 이미지와 정보를 교환하는 데 사용되며 20년 이상 사용되어 왔습니다. DICOM 형식의 사용은 90년대 중반에 시작.
DICOM에는 이미지 발신자와 수신자가 분석된 이미지에 대한 정보를 교환할 수 있는 여러 지원 계층이 함께 제공

오늘날 CT, MRI, 초음파 및 RF를 포함하여 방사선학에 사용되는 거의 모든 이미징 장치는 DICOM 표준을 지원하도록 장착 [**표준 퍼실리테이터에 따르면**](https://www.nema.org/standards/view/digital-imaging-and-communications-in-medicine) **DICOM은 "멀티 벤더 환경에서 의료 이미지를 전송할 수 있도록 하고 사진 보관 및 통신 시스템의 개발 및 확장을 용이하게 합니다."**

NifTI : 

[**NIfTI(Neuroimaging Informatics Technology Initiative)는**](https://nifti.nimh.nih.gov/background) 의료 및 연구 기기 사용자 및 제조업체와 협력하여 다른 이미징 표준의 일부 문제와 부족함을 해결하기 위해 설립되었습니다. NIfTI 표준은 기능적 자기 공명 영상(fMRI)에 중점을 두고 신경 영상 분야에서 이러한 문제를 해결하기 위해 특별히 설계되었습니다.

신경외과 의사가 구형 이미지 형식, 특히 Analyze 7.5 파일 형식에서 직면한 가장 중요한 문제 중 하나는 이미지 개체의 방향에 대한 정보가 부족하다는 것이었습니다.

방향이 모호하고 불분명했기 때문에 이미지를 분석하려는 사람은 누구나 이미지 내 물체의 방향에 대한 자세한 메모를 추가해야 했습니다. 특히, 의사가 뇌의 어느 쪽을 보고 있는지에 대해 종종 혼란이 있었는데, 이는 해결책이 필요한 중요한 문제였습니다.

# Data Tag 구조 (DICOM)

**Dicom 주요 헤더 정보**

**[ Bits Stored ]**

**Dicom 파일의 이미지 정보가 저장되어 있는 비트(bit)의 수**이다.

dicom은 주로 10~16bit를 사용하여 정보가 저장된다.

즉, dicom 파일을 이루고있는 값들의 range를 알 수 있다.

이 정보가 중요한 이유는, 앞서 말했듯이 딥러닝 모델의 input range를 맞춰주어야 하기 때문이다.

만약 A dicom 파일은 10bit로 저장되어있고, B dicom 파일은 16bit로 저장되어있다면,

A의 maximum value는 $2^{10}-1$ 인 1023일 것이고, B는 $2^{16}-1$인 65535일 것이다.

이처럼 range의 차이가 무시무시하기 때문에 반드시 dicom 파일을 읽어온 이후에는 bits stored를 활용하여 range를 맞춰주어야 한다.

**[ Rescale Slope / Rescale Intercept ]**

Bits stored 정보와 다른 의미로 dicom 파일에 저장된 값을 전처리하기 위해 필요한 정보이다.

컴퓨터에 X-ray 이미지를 저장할 때에는 unsigned int로 저장되기 때문에 음수를 저장할 수 없는 문제가 생긴다.

즉, 이미지의 실제 값(image value)과 저장되는 값(stored value)간의 차이가 존재하게 되는데,

저장된 값으로부터 실제 값을 계산하기 위하여 rescale slope와 rescale intercept를 사용하는 것이다.

즉, **기울기와 비슷한 개념의 "Rescale Slope"**와, **offset과 비슷한 개념의 "Rescale Intercept"**를 헤더에 저장해줌으로서, 아래와 같은 수식을 통해 저장된 값으로부터 실제 이미지 값으로 변경하여 준다.

$Imagevalue=(RescaleSlope)∗(Storedvalue)+(RescaleIntercept)$

예를 들어, CT에서 주로 쓰이는 unit인 HU는 음수값을 포함한다. 따라서 **Rescale Intercept**는 주로 음수부호를 갖는다.

**Rescale Slope**역시 정보저장에 필요한 bit수보다 적은 수의 bit에 모든 정보를 저장하기 위해서 scaling factor로서 필요하다.

**[ Window Center / Window Width ]**

Window center와 window width는 X-ray/CT 이미지에서 특정 조직을 더욱 잘 보이게 강조하기 위한 값이다.

특정 조직들은 일반적으로 갖는 HU값의 범위가 정해져있다.

따라서 window center/window width를 보고자 하는 조직의 HU range로 맞추어 영상에서 그 조직부분을 강조시키는 것이다.

해당 조직이 잘 보이도록 영상을 normalization을 해준다고 보아도 무방하다.

range는 다음과 같은 방식으로 맞춘다.

$range=(WindowCenter− \frac {WindowWidth}{2},WindowCenter+\frac {WindowWidth}{2})$

**[ Photometric Interpretation ]**

Photometric interpretation은 dicom 이미지의 visualization과 관련된 정보이다.

이 헤더의 정보를 이용하지 않을 경우, viewer를 통해 열어본 이미지와 python에서 이미지를 plot했을 때, 색상반전이 있을 수 있다.

값으로는 **"MONOCHROME1", "MONOCHROME2", "RGB"**등이 있다.

**MONOCHROME1:** 최소값을 갖는 pixel이 하얀색으로 나타나는 gray scale image.

**MONOCHROME2:** 최소값을 갖는 pixel이 검정색으로 나타나는 gray scale image.

**RGB:** Color image.

![https://blog.kakaocdn.net/dn/uRZwC/btrbWe1rV7y/ju7DI9ebLeAyR0cnAOL3EK/img.png](https://blog.kakaocdn.net/dn/uRZwC/btrbWe1rV7y/ju7DI9ebLeAyR0cnAOL3EK/img.png)

즉, MONOCHROME1 값을 갖는 이미지는 viewer로 이미지를 열었을 때, 최소값을 갖는 부분인 air 혹은 lung부분이 하얀색이다. (우측그림)

반대로 MONOCHROME2 값을 갖는 이미지는 해당 부분이 검정색을 띈다. (좌측그림)

# Data Tag 구조 (NIfTI)

```
sizeof_hdr      : 540
magic           : b'n+2'
eol_check       : [13 10 26 10]
datatype        : int16
bitpix          : 16
dim             : [ 4 32 20 12  2  1  1  1]
intent_p1       : 0.0
intent_p2       : 0.0
intent_p3       : 0.0
pixdim          : [   -1.      2.      2.      2.2  2000.      1.      1.      1. ]
vox_offset      : 0
scl_slope       : nan
scl_inter       : nan
cal_max         : 1162.0
cal_min         : 0.0
slice_duration  : 0.0
toffset         : 0.0
slice_start     : 0
slice_end       : 23
descrip         : b'FSL3.3\\x00 v2.25 NIfTI-1 Single file format'
aux_file        : b''
qform_code      : scanner
sform_code      : scanner
quatern_b       : -1.94510681403e-26
quatern_c       : -0.996708512306
quatern_d       : -0.081068739295
qoffset_x       : 117.855102539
qoffset_y       : -35.7229423523
qoffset_z       : -7.24879837036
srow_x          : [  -2.      0.      0.    117.86]
srow_y          : [ -0.     1.97  -0.36 -35.72]
srow_z          : [ 0.    0.32  2.17 -7.25]
slice_code      : unknown
xyzt_units      : 10
intent_code     : none
intent_name     : b''
dim_info        : 57
unused_str      : b''

```

1. **sizeof_hdr**: 헤더의 크기를 바이트 단위로 나타냅니다. 여기서는 540바이트입니다.
2. **magic**: 파일 형식의 식별자입니다. **`b'n+2'`**는 NIfTI-2 파일을 나타냅니다.
3. **eol_check**: 파일의 끝(end-of-line)을 확인하기 위한 문자열입니다. **`[13 10 26 10]`**은 줄바꿈과 파일 종료를 나타내는 ASCII 코드입니다.
4. **datatype**: 데이터 타입을 나타냅니다. **`int16`**은 16비트 정수형 데이터를 의미합니다.
5. **bitpix**: 한 데이터 포인트를 저장하는 데 사용된 비트 수입니다. 여기서는 16비트입니다.
6. **dim**: 데이터 배열의 차원을 나타냅니다. **`[4 32 20 12 2 1 1 1]`**은 4차원 데이터이며, 각 차원의 크기는 32, 20, 12, 2입니다.
7. **intent_p1, intent_p2, intent_p3**: 통계적 파라미터의 목적을 위한 필드입니다. 여기서는 모두 0.0으로 설정되어 있습니다.
8. **pixdim**: 각 차원의 픽셀 또는 복셀의 크기를 나타냅니다. 첫 번째 값 **`1`**은 시간 축의 방향성을 나타내며, 나머지는 각 공간 차원의 크기를 미터 단위로 나타냅니다.
9. **vox_offset**: 영상 데이터가 파일 내에서 시작되는 위치의 오프셋입니다. 여기서는 0입니다.
10. **scl_slope, scl_inter**: 데이터 스케일링을 위한 기울기와 절편입니다. **`nan`**은 정의되지 않았음을 의미합니다.
11. **cal_max, cal_min**: 영상 데이터의 최대 및 최소 표시 가능 값입니다. 여기서는 최대값이 1162.0, 최소값이 0.0입니다.
12. **slice_duration**: 각 슬라이스의 취득 시간입니다. 여기서는 0.0으로 설정되어 있습니다.
13. **toffset**: 시간 축의 오프셋입니다. 여기서는 0.0입니다.
14. **slice_start, slice_end**: 슬라이스 스캔의 시작과 끝을 나타냅니다. 여기서는 0부터 23까지입니다.
15. **descrip**: 영상에 대한 추가적인 설명입니다. 여기서는 **`FSL3.3\x00 v2.25 NIfTI-1 Single file format`**으로 FSL 소프트웨어 버전과 NIfTI 파일 형식에 대한 정보를 포함하고 있습니다.
16. **aux_file**: 보조 파일의 이름입니다. 여기서는 비어 있습니다(**`b''`**).
17. **qform_code, sform_code**: 공간적 좌표계를 정의하는 코드입니다. **`scanner`**는 스캐너의 기본 좌표계를 사용함을 의미합니다.
18. **quatern_b, quatern_c, quatern_d, qoffset_x, qoffset_y, qoffset_z**: quaternion 매개변수와 오프셋으로, 3D 공간에서의 위치와 방향을 정의합니다.
19. **srow_x, srow_y, srow_z**: 공간 변환을 위한 매트릭스의 행입니다. 이는 영상 데이터를 실제 공간 좌표계로 변환하는 데 사용됩니다.
20. **slice_code**: 슬라이스 타이밍 정보의 유형을 나타냅니다. **`unknown`**은 정의되지 않았음을 의미합니다.
21. **xyzt_units**: 공간 및 시간의 단위를 나타냅니다. **`10`**은 공간 단위가 mm, 시간 단위가 초임을 의미합니다.
22. **intent_code**: 데이터의 통계적 또는 특정 목적을 나타내는 코드입니다. **`none`**은 특별한 목적이 없음을 의미합니다.
23. **intent_name**: 특정 목적의 이름입니다. 여기서는 비어 있습니다(**`b''`**).
24. **dim_info**: 차원에 대한 정보를 나타내는 코드입니다. 여기서는 57입니다.
25. **unused_str**: 사용되지 않는 문자열입니다. 여기서는 비어 있습니다(**`b''`**).