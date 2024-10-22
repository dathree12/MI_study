# 3. Chest X-ray 데이터 구조

Modality: CXR
Created by: 건희 이

# DICOM(Digital Imaging and Communications in Medicine)

- 의료용 기기에서 디지털 영상자료를 표현하거나 디지털 영상자료를 이용해 통신할 때 지켜야 할 표준 포맷
- 발표
    - 1983: ARC(American College of Radiology)&NEMA(National Electrical Manufacturers Association)에서 ACR-NEMA 디지털 영상전송 표준 위원해 발족
    - 1985: 버전 1.0 RSNA(Radiological Society of North America)에서 첫 발표
    - 1988: 버전 2.0 RSNA 발표
    - 1992: RSNA 회의에서 DICOM 명칭 제안
    - 1993: 첫 데모 버전 발표
- 확장자: .dcm

# DICOM 파일 구성

- File Meta Information
- Object Instance (Information Object)

<img src="/1_CXR/1-3_Chest X-ray 데이터 구조/Untitled.png"></img><br/>

이미지 출저: https://www.opswat.com/blog/opswat-deep-cdr-now-supports-dicom-file-format

<img src="/1_CXR/1-3_Chest X-ray 데이터 구조/Untitled 1.png"></img><br/>

이미지 출처: https://www.sciencedirect.com/topics/computer-science/formatted-file

<img src="/1_CXR/1-3_Chest X-ray 데이터 구조/Untitled 2.png"></img><br/>

이미지 출처: [https://www.leadtools.com/help/sdk/v21/dicom/api/overview-basic-dicom-file-structure.html](https://www.leadtools.com/help/sdk/v21/dicom/api/overview-basic-dicom-file-structure.html)

### File Meta Information

- File Preamble: 128 byte의 dummy byte로 주로 zero 값으로 구성되어 있지만 필요시 정보를 담을 수 있음
- DICOM prefix: preamble 다음에 “DICM”이 나와야 DICOM임을 확인
- File Meta Elements

<img src="/1_CXR/1-3_Chest X-ray 데이터 구조/Untitled 3.png"></img><br/>

이미지 출처: [https://slidesplayer.org/slide/12934045/](https://slidesplayer.org/slide/12934045/)

### Object Instance (Information Object)

- IOD(Information Object Definition) 형식
    - IOD: Information Entities, Modules, Attributes로 구성
    - ex. CXR을 하나의 객체(Information Object)로 설정하고, 객체를 여러 entities를 이용해 표현 (환자정보 entity, 검사정보 entity, 이미지정보 entity)
    각 entity는 해당 entity에 어울리는 데이터 종류(Module)로 구성 (환자정보 entity - 나이, 체중, 이름 module)
    Module의 실질적 값이 attribute
        
        <img src="/1_CXR/1-3_Chest X-ray 데이터 구조/Untitled 4.png"></img><br/>
        
        이미지 출처: [https://libertegrace.tistory.com/m/entry/Medical-Image-DICOMDigital-Imaging-and-Communications-in-Medicine?category=905970](https://libertegrace.tistory.com/m/entry/Medical-Image-DICOMDigital-Imaging-and-Communications-in-Medicine?category=905970)
        
        <img src="/1_CXR/1-3_Chest X-ray 데이터 구조/Untitled 5.png"></img><br/>
        
        이미지 출처: [https://slidesplayer.org/slide/12934045/](https://slidesplayer.org/slide/12934045/)
        
- data element: 실질적 data (attributes)로 tag를 통해 구분

        <img src="/1_CXR/1-3_Chest X-ray 데이터 구조/Untitled 6.png"></img><br/>

이미지 출처: [https://towardsdatascience.com/understanding-dicom-bce665e62b72?gi=c2adb34ff1a5](https://towardsdatascience.com/understanding-dicom-bce665e62b72?gi=c2adb34ff1a5)

# DICOM viewer

- MicroDicom DICOM viewer, Sante DICOM viewer 등
    
    <img src="/1_CXR/1-3_Chest X-ray 데이터 구조/Untitled 7.png"></img><br/>
    
    MicroDicom viewer로 본 Chest AP
