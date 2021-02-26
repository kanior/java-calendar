# 비정형서식 XML 작성 가이드

> 본 가이드는 아래의 버전을 기준으로 작성 되었음.
>
> - 모듈 버전: 1.2.0 이상
> - 서식xml 버전: 0.5.0 이상

## 0. 목차

[TOC]

## 1. 시작하기

todo: 동작 개념 설명, 테스트 방법, 로그 남기기, 로그 보는 방법

배포된 모듈을 받았다고 가정...

## 2. 서식 XML 파일 구성

서식 XML 파일은 크게 3 종류의 파일로 구성 할 수 있다.

- 서식처리 정보 파일: 가장 기본이 되는 파일이다.  서식처리를 위한 핵심 정보가 기록된다.
- 서식처리 정보 파일들을 include 하는 파일: 여러개의 서식파일 경로를 저장한 파일이다.
- 문자열 교정정보 파일: 서식처리할 때 사용되는 문자열 교정정보를 모아놓은 파일이다.
- 서식처리 환경정보 파일: 서식을 처리하기 위해서 필요한 환경정보(이미지 전처리 등)를 저장한 파일이다.

### 2-1. 서식처리 정보 파일

 가장 기본이 되는 파일이다.  서식처리를 위한 핵심 정보가 기록된다. 하나의 파일에 여러 종류의 서식내용을 포함할 수 있다. 하지만 관리를 쉽게하기 위해서는 한 파일에 한 종류의 서식만 포함하는 것이 바람직하다.

아래와 같이 `<FormSet>` 태그 하위에 정의할 서식 개수 만큼 `<Form>` 태그를 사용해서 기술한다.

서식처리 모듈에서는 모든 `<Form>`태그의 서식내용을 처리하고 매칭도가 가장 높은 `<Form>` 의 결과를 출력한다.

```xml
<?xml version="1.0" encoding="utf-8" standalone="yes"?>

<IzFormOcrXml version="0.5.0"> <!-- 서식파일 버전-->

  <Comment>서식 파일에 대한 주석</Comment>

  <!-- 서식처리에 적용되는 환경설정 정보 -->
  <FormConfiguration>
  ...
  </FormConfiguration>

  <!-- 서식처리에 적용되는 환경설정파일 정보
    <FormConfiguration> 또는 <FormConfigurationFile> 둘 중 하나의 방식으로 환경설정정보를 설정가능하다.
    -->
  <FormConfigurationFile>form_config.xml</FormConfigurationFile>

  <!-- 서식분류 정보 -->
  <FormClassification>
      ...
  </FormClassification>

  <!-- 서식내용 시작 태그. FormSet태그는 파일내에서 한번만 사용하며 이 태그 하위에 필요한 모든 서식내용을 기술 한다. -->
  <FormSet>

    <!-- Form 태그는 한 종류의 문서서식을 정의할 때 사용.
      여러종류의 문서서식을 정의할 경우에는 각 서식마다 별도의 Form 태그를 선언하고 내용을 기술.
      FormSet 태그 하위에는 1개 이상의 Form 태그 사용가능
    -->
    <Form>
      <Comment>문서서식에 대한 주석</Comment>

      <!-- FormPage 태그는 페이지서식을 정의할 때 사용.
      여러종류의 페이지서식을 정의할 수 있다.
      -->
      <FormPage> <!-- 서식내용 --> </FormPage>
      ...
      <FormPage> ... </FormPage>
    </Form>

    <!-- 두번 째 문서서식 -->
    <Form> ... </Form>
    ...
    <!-- n번 째 문서서식 -->
    <Form> ... </Form>
  </FormSet>
</IzFormOcrXml>
```

### 2-2. 서식처리 정보 파일들을 include 하는 파일

여러개의 서식파일을 서식처리 하기 위해서 사용한다. 사용할 서식파일들의 경로를 하나의 파일에 include한 후 모듈에 서식파일로 설정하면 include된 모든 서식파일의 정보를 읽어서 처리한다. 파일 구조는 아래와 같다.

```xml
<?xml version="1.0" encoding="utf-8" standalone="yes"?>

<!-- 사용할 서식 파일을 지정. 상대경로를 사용할 것. -->
<IzFormInclude version="0.0.2">

  <!-- 서식처리에 적용되는 환경설정 정보 -->
  <FormConfiguration>
  ...
  </FormConfiguration>

  <!-- 서식처리에 적용되는 환경설정파일 정보
    <FormConfiguration> 또는 <FormConfigurationFile> 둘 중 하나의 방식으로 환경설정정보를 설정가능하다.
    -->
  <FormConfigurationFile>form_config.xml</FormConfigurationFile>

  <!-- 서식분류 정보 -->
  <FormClassification>
      ...
  </FormClassification>

  <FormPath>form_1.xml</FormPath>
  <FormPath>form_2.xml</FormPath>
  <FormPath>form_3.xml</FormPath>
      ...
  <FormPath>form_N.xml</FormPath>
</IzFormInclude>
```

### 2-3. 문자열 교정정보 파일

서식처리할 때 사용되는 문자열 교정정보를 모아놓은 파일이다. 기본적으로 문자열 교정정보는 서식처리 정보파일에 포함할 수 있다. 하지만 교정정보가 많아지면 서식정보 파일에 포함하기 보다는 별도의 파일로 구성하는 것이 바람직하다. 서식정보파일에서는 사용할 교정정보 파일을 지정하면 된다. 파일 구조는 아래와 같다.

```xml
<?xml version="1.0" encoding="utf-8" standalone="yes"?>

<!-- 문자열 교정정보 태그. 파일내에서 한번만 사용 -->
<CorrectionInfo>

    <!-- 교정정보 세트
        CorrectionInfo 태그 하위에는 여러개의 교정정보 세트를 포함할 수 있다.
        각 교정정보 세트는 id를 지정해야 한다.
        서식처리정보 파일에서는 파일명과 교정정보 세트 id로 접근 할 수 있다. -->
    <CorrectionDataSet id="set1">

        <!-- 한 문자열 교정정보 태그 -->
        <CorrectionData>
            <!-- 교정할 문자열 탐색정보와 수정할 문자열
            SearchText의 정보로 문자열을 탐색하고 NewText의 문자열로 교체한다. -->
            <SearchText type="text" similarity="90">주변긔기</SearchText>
            <NewText type="text">주변기기</NewText>
        </CorrectionData>

        <CorrectionData>
            <!-- SearchText는 필요한 만큼 포함할 수 있다.
            한개라도 일치되는 것을 찾았다면 NewText로 교체된다.
            당연히 NewText는 반드시 하나만 있어야 한다. -->
            <SearchText type="text" similarity="100">겅기도</SearchText>
            <SearchText type="text" similarity="100">깅기도</SearchText>
            <SearchText type="text" similarity="100">겅기드</SearchText>
            <NewText type="text">경기도</NewText>
        </CorrectionData>
        ...
        <CorrectionData>내용</CorrectionData>
    </CorrectionDataSet>

    <CorrectionDataSet id="set2"> ... 내용 ...  </CorrectionDataSet>
    ...
    <CorrectionDataSet id="setN"> ... 내용 ...  </CorrectionDataSet>
</CorrectionInfo>
```

서식처리 정보 파일에서 교정정보 파일 지정 방법은 아래와 같다.

```xml
<Element id="elem1" type="CharacterString">

  <!-- 후처리정보에서 교정정보파일을 지정한다. -->
  <!-- 참고: 후처리정보는 CharacterString element에서만 사용가능 -->
  <PostprocessInfo>
    <CorrectionInfo>
      <!-- 교정정보파일은 CorrectionDataInclude 태그 안쪽에 기술한다. -->
      <CorrectionDataInclude>
        <!-- Include 태그로 파일경로와 교정세트 id를 지정한다.
          교정세트 id를 "*"로 지정하면 파일내의 모든 교정세트를 사용한다는 의미이다.
          교정파일은 2개 이상 필요한 만큼 Include 할 수 있다.
        -->
        <Include xmlpath="Correction_1.xml" correctionSetId="*"/>
        <Include xmlpath="Correction_2.xml" correctionSetId="set1"/>
        <!-- 같은 파일의 다른 세트를 지정하는 것도 가능 -->
        <Include xmlpath="Correction_2.xml" correctionSetId="set2"/>
      </CorrectionDataInclude>
    </CorrectionInfo>
  </PostprocessInfo>
```

### 2-4. 서식처리 환경설정정보 파일

서식처리에 필요한 정보를 설정하는 파일이다. 이 파일에 설정된 정보가 서식처리할 때 공통으로 적용된다. 설정가능한 정보는 아래와 같다.

- 관심영역
- 이미지 축소 해상도
- skew 보정 수행 유무
- 이미지 회전방향 정보
- 이미지전처리
- 텍스트영역 추출 옵션
- OCR 옵션

설정을 위해서는 별도의 xml파일에 환경설정정보를 저장 후 다른 서식파일에서 링크하는 방식과 환경설정정보 자체를 서식파일에 바로 포함시키는 방법이 가능하다.

#### 2-4-1. 환경설정정보 링크

아래와 같은 내용의 설정정보파일을 "form_conf.xml"로 저장했다고 하자.

```xml
<?xml version="1.0" encoding="utf-8" standalone="yes"?>

<!-- 사용할 서식 파일을 지정. 상대경로를 사용할 것. -->
<FormConfiguration>
  <!-- 관심영역 설정
    관심영역은 서식인식에 사용할 이미지의 범위를 의미함.
    관심영역내에서만 필요한 데이터를 추출하고 인식을 수행함.
    따라서 필요한 데이터만 포함하는 최소영역을 지정하면 처리정확도가 높아지고 처리시간이 단축됨.
    설정하지 않거나 모든 값이 0 또는 "" 인 경우에는 전체이미지가 관심영역으로 설정됨
  -->
  <!--
  <Roi left="0" top="0" right="0" bottom="0"/>
  -->

  <!-- 이미지 축소 해상도 (단위: pixel)
    너무 큰 이미지가 입력되면 처리시간이 길어지는 문제가 있음.
    이 때 적절한 크기로 이미지를 축소하면 처리시간이 길어지는 문제를 해결 할 수 있음.
    (적절한 크기는 인식률과 처리시간의 관계를 실험 후 결정해야 함.)
    이미지가 이 값보다 크면 지정된 크기로 축소해서 전체프로세스를 처리함.
    0: 원본이미지사용 (default)
    최소값: 1000000 (1M pixels). 안정성을 위해서 이 값보다 작으면 이 값으로 고정됨.
  -->
  <!--
  <ReduceImageSize>0</ReduceImageSize>
  -->

  <!-- skew 보정 수행유무
      - true : skew 보정 수행 (default)
      - false : skew 보정 수행 안함
  -->
  <!--
  <SkewCorrection>true</SkewCorrection>
  -->

  <!-- 이미지 회전방향 정보.
    회전방향 정보에 따라서 회전보정 후 서식인식을 수행하게됨.
    2개 이상 회전방향도 설정가능하며 이 때에는 회전방향 자동판별 후 회전보정 처리됨.
      - 입력가능 값(시계 반대방향 각도) : 0, 90, 180, 270
      - 2개 이상 회전방향 설정 방법 : "0|90", "0|90|180|270" ...
      - default는 0
  -->
  <!--
  <ImageOrientation>0|90|180|270</ImageOrientation>
  -->

  <ImageProcessInfo>
    <ImageProcessDefine>
      <!-- 이미지 전처리 옵션 정의 -->
      <ImageProcess id="denoise_amf" type="denoise">
        <Parameters method="adaptiveMedianFilter" windowSize="3"/>
      </ImageProcess>
    </ImageProcessDefine>

    <!-- 이미지 전처리 수행 제어 -->
    <ImageProcessExec id="denoise_amf_" disabled="false" order="denoise_amf"/>
  </ImageProcessInfo>

  <ExtractTextRegion disabled="true" documentType="document" subOption="table" reduceRatio16="16" inverseType="0"/>

  <RecogString disabled="true" ocrEngine="inzi" language="korean" charSet=""/>
</FormConfiguration>
```

서식처리 정보파일과 서식처리 정보파일들을 include 하는 파일에 위의 환경정보파일을 링크하면 된다.

- 서식처리 정보파일들을 include 하는 파일에서 환경정보 파일 링크

```xml
<?xml version="1.0" encoding="utf-8" standalone="yes"?>

<!-- 사용할 서식 파일을 지정. 상대경로를 사용할 것. -->
<IzFormInclude version="0.0.2">

  <FormConfigurationFile>form_config.xml</FormConfigurationFile>

  <FormPath>form_1.xml</FormPath>
  <FormPath>form_2.xml</FormPath>
  <FormPath>form_3.xml</FormPath>
      ...
  <FormPath>form_N.xml</FormPath>
</IzFormInclude>
```

- 서식처리 정보 파일에서 환경정보 파일 링크

```xml
<?xml version="1.0" encoding="utf-8" standalone="yes"?>

<IzFormOcrXml version="0.5.0"> <!-- 서식파일 버전-->

    <Comment>서식 파일에 대한 주석</Comment>

    <FormConfigurationFile>form_config.xml</FormConfigurationFile>

    <!-- 서식내용 시작 태그. FormSet태그는 파일내에서 한번만 사용하며 이 태그 하위에 필요한 모든 서식내용을 기술 한다. -->
    <FormSet>
    ...
    </FormSet>
</IzFormOcrXml>
```

#### 2-4-2. 환경설정정보를 서식파일에 포함하는 방식

- 서식처리 정보파일들을 include 하는 파일에서 환경정보 적용방식

아래와 같이 `<FormConfiguration>` 을 이용해서 환경정보를 설정할 수 있다.

```xml
<?xml version="1.0" encoding="utf-8" standalone="yes"?>

<!-- 사용할 서식 파일을 지정. 상대경로를 사용할 것. -->
<IzFormInclude version="0.0.2">

  <!-- 서식처리에 적용되는 환경설정 정보 -->
  <FormConfiguration>
  ...
  </FormConfiguration>

  <!-- 서식처리에 적용되는 환경설정파일 정보
    <FormConfiguration> 또는 <FormConfigurationFile> 둘 중 하나의 방식으로 환경설정정보를 설정가능하다.
    -->
  <FormConfigurationFile>form_config.xml</FormConfigurationFile>

  <FormPath>form_1.xml</FormPath>
  <FormPath>form_2.xml</FormPath>
  <FormPath>form_3.xml</FormPath>
      ...
  <FormPath>form_N.xml</FormPath>
</IzFormInclude>
```

- 서식처리 정보 파일에서 환경정보 적용방식

아래와 같이 `<FormConfiguration>` 을 이용해서 환경정보를 설정할 수 있다.

```xml
<?xml version="1.0" encoding="utf-8" standalone="yes"?>

<IzFormOcrXml version="0.5.0"> <!-- 서식파일 버전-->

    <Comment>서식 파일에 대한 주석</Comment>

    <!-- 서식처리에 적용되는 환경설정 정보 -->
    <FormConfiguration>
    ...
    </FormConfiguration>

    <!-- 서식내용 시작 태그. FormSet태그는 파일내에서 한번만 사용하며 이 태그 하위에 필요한 모든 서식내용을 기술 한다. -->
    <FormSet>
    ...
    </FormSet>
</IzFormOcrXml>
```

#### 2-4-3. 환경설정정보 적용방법

환경설정정보는 직접 호출하는 서식 xml의 정보만 적용된다. 아래의 case를 참고 바란다.

case 1. 환경설정정보가 include 서식(form_inc.xml)과 메인서식(form_1.xml) 두 부분에 정의 되어 있을 경우에 form_inc.xml을 로드하게 되면 form_inc.xml의 환경설정정보가 적용된다. form_1.xml의 환경설정정보는 무시된다.

- form_inc.xml

```xml
<?xml version="1.0" encoding="utf-8" standalone="yes"?>

<!-- form_inc.xml -->
<IzFormInclude version="0.0.2">

  <FormConfigurationFile>form_config_a.xml</FormConfigurationFile>

  <FormPath>form_1.xml</FormPath>
</IzFormInclude>
```

- form_1.xml

```xml
<?xml version="1.0" encoding="utf-8" standalone="yes"?>

<!-- form_1.xml -->
<IzFormOcrXml version="0.5.0">

  <FormConfigurationFile>form_config_b.xml</FormConfigurationFile>

</IzFormOcrXml>
```

case 2. 환경설정정보가 메인서식에만 존재하는 경우에 form_inc.xml을 로드한다면 form_1.xml과 form_2.xml 모두 환경설정정보가 무시된다. default 값이 적용된다. 만약 form_1.xml을 직접 로딩하는 경우에는 form_1.xml 환경설정정보가 적용된다.

- form_inc.xml

```xml
<?xml version="1.0" encoding="utf-8" standalone="yes"?>
<IzFormInclude version="0.0.2">
  <FormPath>form_1.xml</FormPath>
  <FormPath>form_2.xml</FormPath>
</IzFormInclude>
```

- form_1.xml

```xml
<?xml version="1.0" encoding="utf-8" standalone="yes"?>
<IzFormOcrXml version="0.5.0">
  <FormConfigurationFile>form_config_a.xml</FormConfigurationFile>
</IzFormOcrXml>
```

- form_2.xml

```xml
<?xml version="1.0" encoding="utf-8" standalone="yes"?>
<IzFormOcrXml version="0.5.0">
  <FormConfigurationFile>form_config_b.xml</FormConfigurationFile>
</IzFormOcrXml>
```

## 3. 서식작성 전략

### 탐색영역 설정

## 4. 태그 설명

### `<IzFormOcrXml>`

서식 xml 파일의 root 태그다.

- 속성

  `version`: 버전 정보. 반드시 존재해야 한다. 버전 정보가 없거나 틀리면 사용할 수 없으므로 임의로 수정하면 안된다. 아래의 방법으로 정확한 버전을 확인 후 기술해야 한다.

  > TODO: 내용작성

  _서식인식 모듈에서 사용가능한 서식 xml 버전 확인할 것_

  버전의 각 자리 숫자의 의미는 아래와 같다.

  `major.minor.patch`

  - major: 주요컨셉이 변경되는 등 큰 변화가 있을 때 증가. 하위호환 불가
  - minor: 하위호환 불가능한 수정이 된 경우
  - patch: 하위호환 가능한 수정이 된 경우

---

### `<FormSet>`

서식내용 시작을 알린다. XML 파일내의 모든 서식처리 정보는 이 태그 내에 기록된다. XML 파일내에서는 한번만 사용한다.

- 하위 태그

  `<Form>`: 서식내용을 정의할 수 있다. `<Form>`내부에는 한 종류의 서식을 정의 할 수 있으며 여러종류의 서식을 정의 하려면 각 서식마다 `<Form>` 을 추가하면 된다.

---

### `<Form>`

`<form>` 태그는 하나의 서식을 처리하기 위한 모든 내용을 기술 할 때 사용한다.

```XML
<form id="" revision="" disabled="false">
  <FormName></FormName> <!-- 서식 이름 -->
  <FormConfig></FormConfig> <!-- 서식 환경설정 -->
  <Comment>문서서식에 대한 주석</Comment>
  <FormPage></FormPage> <!-- 페이지 서식 -->
    ...
  <FormPage></FormPage>
</form>
```

- 속성

  `id`:  서식 id는 영숫자이며 서식을 구분하기 위해 사용한다. 서식처리과정에 영향을 주지 않는다. 최종 서식처리결과에 출력되며, 서식처리로그 파일명을 결정하며, 서식처리로그 내용에 기록된다.

  `revision`: 서식 개정 번호이며 숫자를 사용한다. 서식의 내용이 수정, 변경되면 숫자를 증가 시켜준다.

  `disabled`: 서식처리 유무를 지정한다. true면 사용하지 않고 false면 사용한다. default는 false 이다.

- 하위태그

  `<FormName>`: 서식 이름이며 영숫자, 한글 문자로 기술 가능하다. 서식처리과정에 영향을 주지 않는다. 최종 서식처리결과에 출력되며, 서식처리로그 내용에 기록된다.

  `<FormConfig>`: 현재 서식의 환경설정 정보를 기록한다.

  `<FormPage>`: 페이지서식 정보를 기록한다.

---

### `<FormConfig>`

서식의 환경정보를 설정한다.

```XML
<FormConfig>
    <Disabled>
        <FormPage id="page1" disabled="false">
            <FormIdentification disabled="false"/>
            <FormData id="1" disabled="false"/>
        </FormPage>
        <FormPage id="page2" disabled="false">
            <FormIdentification disabled="false"/>
            <FormData id="1" disabled="false"/>
        </FormPage>
        ...
    </Disabled>
</FormConfig>
```

**하위태그**
`<Disabled>`: 서식내용 중 일부를 사용하지 않도록 제어할 때 사용한다. `<FormPage>`, `<FormIdentification>`, `<FormData>` 정보를 제어할 수 있으며 사용하지 않는 경우에는 `disabled`속성을 true로 지정하면 된다. default는 false이다.

---

### `<FormPage>`

페이지서식을 처리하기 위한 모든 내용을 기술 할 때 사용한다.

```XML
<FormPage id="page1" header="false" footer="false">
  <Comment>페이지서식에 대한 주석</Comment>
  <FormIdentification></FormIdentification> <!-- 서식 식별 정보 -->
  <FormData></FormData> <!-- 서식 처리 정보. 필요한 만큼 반복사용가능 -->
  ...
  <FormData></FormData>
</form>
```

- 속성

  `id`:  영숫자이며 서식을 구분하기 위해 사용한다. 서식처리과정에 영향을 주지 않는다. 최종 서식처리결과에 출력되며, 서식처리로그 파일명을 결정하며, 서식처리로그 내용에 기록된다.

  `header`: 현재 페이지가 헤더(첫번 째 페이지)인지 지정한다.  문서분류를 수행할 때 헤더 페이지를 시작으로 문서를 그룹화 한다.

  `footer`: 현재 페이지가 마지막 페이지인지 지정한다. 문서분류를 수행할 때 마지막 페이지까지 문서를 그룹화 한다. 단 첫번 째 페이지가 발견된 경우에만 적용된다.

- 하위태그

  `<FormIdentification>`: 서식식별(판별)용 정보

  `<FormData>`: 서식 처리 정보

---

### `<FormIdentification>`

서식식별 (판별) 정보를 저장한다.

서식식별 정보는 본격적인 서식필드 추출을 수행하기 전에 처리대상문서인지를 빠르게 파악하기 위한 용도로 사용된다. 모듈에서는 제일 먼저 서식식별 정보를 바탕으로 처리대상 문서 유무를 파악하고 처리대상인 경우에만 서식필드 추출을 수행한다.

필요가 없으면 기술하지 않아도 되며, 이 때에는 처리대상 문서로 간주하고 서식처리를 수행하게 된다.

서식식별은 문서내에 특정문구 존재 유무를 검사하는 것이다. 검사용 문구를 정의하기 위해서 아래와 같이 하위 태그가 구성된다.

```xml
<FormIdentification>
  <TextIdentificationComplex>
    <Element id="100" type="StaticText"></Element>
    <Element id="101" type="StaticText"></Element>
    ...
  </TextIdentificationComplex>

  <TextIdentificationComplex>
    <Element id="103" type="StaticText"></Element>
    ...
  </TextIdentificationComplex>

  ...

  <TextIdentificationComplex>
  </TextIdentificationComplex>
</FormIdentification>
```

- 하위태그

  `<TextIdentificationComplex>`: 검사문구를 정의할 때 사용한다. 최소한 하나 이상 검사문구가 검색되어야 처리 대상 문서로 간주한다. `<TextIdentificationComplex>` 하위에 StaticText타입의 `<Element>` 를 사용해서 검사문구를 정의할 수 있다. `<Element>` 마다 하나의 검사문구를 정의 할 수 있으며 `<TextIdentificationComplex>` 내의 모든 `<Element>` 의 검사문구가 검색성공해야 `<TextIdentificationComplex>` 하나가 검색성공한 것으로 간주한다.

---

### `<FormData>`

서식처리를 위한 핵심 내용을 기술 할 때 사용한다.

```xml
<FormData id="">
  <Element></Element>
  ...
  <Element></Element>

  <OutBlock></OutBlock>
  ...
  <OutBlock></OutBlock>
</FormData>
```

- 속성

  `id`: id는 영숫자이며 formData를 구분하기 위해 사용한다. 서식처리과정에 영향을 주지 않는다. 서식처리로그 파일명에 표시되며, 서식처리로그 내용에 기록된다.

- 하위태그

  `<Element>` 태그는 문서이미지에서 탐색하고자 하는 기본요소를 정의할 때 사용하며 `<OutBlock>`태그는 최종 출력할 필드정보를 정의할 때 사용한다.  각 정보의 쓰임새에 대한 이해를 돕기 위해 예를 들어 보자. 주민등록등본에서 세대주 성명을 찾는다고 가정하자.

![주민등록등본](figure_1.jpg "주민등록등본 이미지")
그림1. 주민등록등본의 세대주 성명 위치

여러 방법이 가능하지만 가장 기본적인 방법으로 키워드 "세대주 성명(한자)" 를 찾고 키워드 오른쪽에서 이름을 찾는다고 하자. 이 때 키워드 "세대주 성명(한자)"와 이름을 각 `<Element>`로 정의하고  `<OutBlock>` 에는 최종 추출할 필드인 이름 `<Element>`를 지정하면 된다.

```xml
<FormData>
  <Element id="1">
    ...세대주 성명 키워드 정의. 세대주 성명문구를 등록...
  </Element>
  <Element id="2">
    ...이름 특징 정의. 세대주 성명 키워드(id=1) 오른쪽에 위치한다는 정보 기술...
  </Element>

  <OutBlock> ...이름이 정의된 element 지정. id=2... </OutBlock>
</FormData>
```

서식처리엔진에서는 이 서식을 바탕으로 아래와 같은 순서로 진행한다.

- Relation 정보 분석

  - step1. `<OutBlock>`의 정보를 파악한다.

    출력해야할 정보는 `<Element id="2">`에 있다.

  - step2.`<Element id="2">`의 정보를 파악한다.

    이름 특징이 정의 되어 있고, 위치는 세대주성명 키워드 오른쪽에 위치하고 있다.

    세대주성명 키워드는 `<Element id="1">`에 정의되어 있다.

  - step3.`<Element id="1">`의 정보를 파악한다.

    세대주 성명 키워드가 정의 되어 있다. 다른 element와의 관련성 정보는 없다.

    정의된 정보를 바탕으로 탐색을 시작한다.

- 탐색

  분석된 정보로 부터 탐색은 `<Element id="1">` -> `<Element id="2">` 순서로 진행한다.

  - step1.`<Element id="1">` 의 세대주성명 문구를 탐색

  - step2.`<Element id="2">` 의 이름을 탐색

    `<Element id="1">`의 오른쪽에 존재하는 문자열에서 이름특징과 매칭되는 문자열 탐색

  - step3.`<OutBlock>`에 지정한 `<Element id="2">`의 탐색결과를 출력한다.

---

### `<Element>`

`<Element>`는 문서이미지의 탐색가능한 기본요소를 정의할 때 사용한다.

```xml
<Element id="1" type="StaticText">
  <Name>이름키워드</Name>
  <Comment>기본요소에 대한 주석</Comment>
  <SearchControl type="required"></SearchControl>
</Element>
```

아래의 속성을 반드시 기술해야 한다.

| element 속성 | 설명                                                         |
| ------------ | ------------------------------------------------------------ |
| id           | 필수속성. element 식별용 고유 id이며 영숫자, 기호 사용.      |
| type         | 필수속성. 기본요소 타입 (StaticText, CharacterString, Column, FormUnit, Merge, Region) |

기본요소는 아래와 같다.

| 기본요소        | 설명                                                         |
| --------------- | ------------------------------------------------------------ |
| StaticText      | 고정된 텍스트 문구. 탐색대상 필드를 찾기위한 키워드 문구를 지정할 때 사용. |
| CharacterString | 문자열. 탐색대상 필드를 지정할 때 사용.                      |
| Column          | 수직방향으로 나열된 리스트. 표의 열 정보를 지정할 때 사용.   |
| FormUnit        | 독립된 영역의 서식을 지정할 때 사용.                         |
| Merge           | 필드의 내용을 결합할 때 사용                                 |
| Region          | 영역을 지정할 때 사용                                        |
| ContentMapping  | 텍스트 맵핑할 때 사용                                        |
| MachineLearning | 딥러닝기반 필드영역 탐색에 사용                               |

- 하위태그

  `<Name>`: element 이름. 한번만 기술

---

#### `StaticText, CharacterString element`

텍스트 탐색을 위한 엘리먼트다. StaticText와 CharacterString 이 있으며 대부분의 내용을 공유한다.  CharacterString의 경우 몇 가지 정보를 더 포함한다.

StaticText는 키워드와 같은 고정된 텍스트 문구를 탐색하기 위해서 사용하며 CharacterString은 탐색대상 필드를 탐색하기 위해서 사용한다.

element의 전체 구성과 사용가능한 하위 태그는 아래와 같다.

```xml
<Element type="StaticText">
    <Name></Name>

    <!-- 탐색 영역 -->
    <SearchRegionInfo>
        <SearchRegionComplex>

            <SearchRegionSet>
                <SearchRegion> ... </SearchRegion>
            </SearchRegionSet>
        </SearchRegionComplex>
    </SearchRegionInfo>

    <!-- 탐색을 위한 전처리
      좌우텍스트 영역 결합, 텍스트 교정
    -->
    <PreprocessInfo>
        <ImagePreprocessInfo/>
        <ExtractTextRegion/>
        <RecogString/>
        <MergeTextRegionOnALine>false</MergeTextRegionOnALine>
        <CorrectionInfo/>
    </PreprocessInfo>

    <!-- 탐색 필드 정보 -->
    <SearchFieldInfo>
        <SearchTextSet>
            <SearchText></SearchText>
            <SearchText></SearchText>
        </SearchTextSet>
        <StringLengthRange> ... </StringLengthRange>
        <RowCountRange> ... </RowCountRange>
    </SearchFieldInfo>
</Element>

<Element type="CharacterString">
    <Name></Name>

    <!-- 필드의 내용 -->
    <Content>
        <SubContent></SubContent>
    </Content>

    <!-- 언어정보 -->
    <LanguageInfo>
        <Language></Language>
        <CharacterSet></CharacterSet>
    </LanguageInfo>

    <!-- 탐색된 필드 후처리
      재인식, 텍스트 교정, 스페이스 제거, 보안처리, 영역 조정
    -->
    <PostprocessInfo>
        <RecogString/>
        <CorrectionInfo></CorrectionInfo>
        <RemoveSpace></RemoveSpace>
        <Security></Security>
        <AdjustFieldRegion></AdjustFieldRegion>
    </PostprocessInfo>

    <ContentMapInfo>
        <ContentValueList>
            <ContentValue>...</ContentValue>
            <ContentValue>...</ContentValue>
            ...
            <ContentValue>...</ContentValue>
        </ContentValueList>
    </ContentMapInfo>

    ... 나머지는 static text의 내용과 동일 ...
</Element>
```

공통적으로 아래와 같이 크게 네개의 섹션으로 구성된다.

- SearchRegionInfo: 탐색영역을 지정한다.
- PreprocessInfo: 탐색영역 내부에 대한 전처리 정보를 지정한다. 필드탐색의 정확도를 높일 수 있는 정보를 설정할 수 있다.
- SearchFieldInfo: 탐색할 필드 정보를 지정한다.
- PostprocessInfo: 탐색된 필드를 후처리하는 정보를 지정한다.
- ContentMapInfo: 탐색된 필드를 교체할 컨텐츠정보를 지정한다.

---

#### `Column element`

list 형태의 텍스트 정보를 추출하기 위해서 사용한다. column element들을 조합하면 표의 텍스트 정보도 추출가능하다. 표 추출 방법은 [표추출방법](###5.1.표추출방법) 를 참고바란다. column element는 아래와 같이 구성된다.

```xml
<Element id="" type="Column">
  <Name></Name>

  <!-- 첫줄의 cell 영역을 지정한다. 이 cell기준 아래쪽으로 필드를 탐색한다. -->
  <FirstCellRegionSet>
    <FirstCellRegion> ...  </FirstCellRegion>
    <FirstCellRegion> ...  </FirstCellRegion>
    ...
    <FirstCellRegion> ...  </FirstCellRegion>
  </FirstCellRegionSet>

  <!-- 탐색할 data의 형태정보 -->
  <ColumnContent type="included"  allownull="false">
  </ColumnContent>

  <!-- 기준 column id: row위치를 참고하기 위한 기준 column 을 지정하면
    기준 column에 맞춰서 row를 추출 함
  -->
  <BaseColumnId></BaseColumnId>

  <!-- column의 하단 마지막 부분 -->
  <ColumnBottom>
  </ColumnBottom>

  <!-- 추출대상 row 개수 -->
  <RowCountRange>
  </RowCountRange>

  <!-- data 필터링 정보 -->
  <RowFilter>
  </RowFilter>
</Element>
```

- FirstCellRegionSet: column의 첫 라인의 영역을 지정한다. 이 영역을 기준으로 아래에 존재하는 필드를 탐색하게 된다. 첫 라인영역은 여러개를 지정할 수 있다. 이 때에는 각 영역을 기준으로 필드를 각각 탐색해 보고 가장 정상적이라고 판단되는 탐색결과 하나만 선택해서 최종 탐색결과로 출력해준다. 영역지정은 `<FirstCellRegion>`태그를 사용하며 [`<SearchRegion>`](#`<SearchRegion>`) 과 동일한 방법으로 지정한다. 아래의 예는 두개의 첫 라인 영역을 지정한 예이다. 각 첫 라인 영역을 기준으로 두 벌의 결과가 탐색되고 좀더 column이 될 가능성이 높은 결과를 출력해 준다. 정상적인 column의 기준은 첫 번째 라인 영역을 기준으로 탐색된 데이터들의 위치가 일정하게 유지되는지를 판단해서 위치가 많이 벗어나지 않는 결과를 선택하게 된다.

  ```xml
  <FirstCellRegionSet>
    <FirstCellRegion>
      <BasePosition type="inElement" baseElementId="itemKeyword-1" x="0" y="100"/>
      <Left unit="elementAvgCharHeightRatio">-1000</Left>
      <Top unit="elementAvgCharHeightRatio">100</Top>
      <Right unit="elementAvgCharHeightRatio">1000</Right>
      <Bottom unit="elementAvgCharHeightRatio">300</Bottom>
    </FirstCellRegion>
    <FirstCellRegion>
      <BasePosition type="inElement" baseElementId="itemKeyword-2" x="0" y="100"/>
      <Left unit="elementAvgCharHeightRatio">-1000</Left>
      <Top unit="elementAvgCharHeightRatio">100</Top>
      <Right unit="elementAvgCharHeightRatio">1000</Right>
      <Bottom unit="elementAvgCharHeightRatio">300</Bottom>
    </FirstCellRegion>
  </FirstCellRegionSet>
  ```

- ColumnContent: 탐색할 data의 형태정보를 string element 타입으로 지정한다. string element에 지정된 정보를 바탕으로 data를 탐색하게 된다. string element는 별도의 element로 구성하고 그 id를 지정하거나 `<ColumnContent>` 태그 하위에 직접 기술할 수 있다.

  ```xml
  <!-- 별도의 string element 를 지정하는 예 -->
  <Element id="columnContent" type="CharacterString">
    ...
  </Element>

  <Element id="items" type="Column">
    ...
    <ColumnContent type="element"  allownull="false" align='left'>
      <ElementId>columnContent</ElementId>
    </ColumnContent>
    ...
  </Element>
  ```

  ```xml
  <!-- 직접 string element 를 기술하는 예 -->
  <ColumnContent type="included"  allownull="false" align='left'>
    <Element id="columnContent" type="CharacterString">
      ...
    </Element>
  </ColumnContent>
  ```

  `<ColumnContent>` 태그는 아래의 3가지 속성을 갖는다.
  - `type`: string element 기술 방식.
    - `element`: element id를 지정
    - `included`: element를 직접 기술
  - `allownull`: column의 data들이 중간에 값이 없는 경우를 허용할지를 설정한다.
    - `true`: 중간에 값이 없는 column 허용
    - `false`: 중간에 값이 모두 채워진 column만 허용
  - `align`: column의 정렬 정보를 지정한다.
    - `left`: 왼쪽 정렬
    - `right`: 오른쪽 정렬
    - `center`: 중간 정렬

- BaseColumnId: 현재 column을 추출할 때 기준이 되는 column을 지정. base column은 현재 column의 data를 추출할 때 각 라인 (cell)의 기준 위치를 참고하기 위해 사용된다. 따라서 `allownull`이 `false`인 모든 cell의 data가 존재하는 column 이어야 한다. base column을 지정해야 하는 예로 재무제표의 경우를 설명해 보자면 금액 column은 중간중간 빠져있는 data가 존재하기 때문에 column의 모든 data를 추출하기가 어렵다. 이때 모든 data가 존재하는 항목column을 base로 지정하면 base column의 각 위치를 참고해서 금액 colum을 모든 data를 빠짐없이 탐색할 수 있게 된다.
- ColumnBottom: column의 하단 마지막 부분을 지정한다. 이 위치를 기준으로 상단의 data만 추출해 준다. 아래와 같이 search region 형태를 사용하여 Bottom 만 지정하면 된다.

  ```xml
  <ColumnBottom>
      <BasePosition type="inElement" baseElementId="name" x="0" y="100"/>
      <Bottom unit="elementAvgCharHeightRatio">5800</Bottom>
  </ColumnBottom>
  ```

- RowCountRange: 추출대상 row의 개수를 지정한다.

  ```xml
  <RowCountRange from="" to=""/>
  ```

  (from, to) 의 범위내에서 추출한다.

  - (,): 개수 제한 없음
  - (0, 0): 개수 제한 없음
  - (2, 2): 2개만 추출
  - (1, 3): 1 ~ 3 추출
  - (0, 3) or (, 3): 0 ~ 3개 추출
  - (2, 0) or (2, ): 최소 2개 이상 추출

- UseLayoutComponent: 미구현
- RowFilter: column의 data 를 필터링 할 수 있는 정보를 지정한다. 필터링 정보에 따라 버려야 하는 data를 제거한다. 아래와 같이 걸러내야 하는 정보를 설정할 수 있다.

  ```xml
  <RowFilter>
    <RowDrop>
      <RowDropComplex>
        <SearchText type="text" similarity="80">주소</SearchText>
      </RowDropComplex>
      <RowDropComplex>
        <SearchText type="regex">주([, .\-1a-z]){0,}소</SearchText>
      </RowDropComplex>
    </RowDrop>
  </RowFilter>
  ```

---

#### `FormUnit element`

> TODO: 내용작성

---

#### `Merge element`

element를 결합한다. 한 element 로 추출하기 어려운 필드는 각 부분을 추출하는 element를 구성한 후 최종단계에서 결합해서 추출할 때 사용가능하다. 예를 들면, 주소의 시도, 구군, 동을 별도의 element로 추출한 후 결합해서 최종 하나의 필드로 출력할 수 있다.

결합하려는 element는 아래와 같은 조건을 만족해야 한다.

- 결합대상 element type: StaticText, CharacterString, Merge
- 결합대상 element의 결과 필드 개수: 1개

merge element 구성:

```xml
<Element id="merge1-3" type="Merge">
  <Name>코드결합</Name>
  <!-- MergeElements 태그에 결합할 element id 정보를 기술한다.
    나열된 순서에 따라 결합된다.
    결합할 때 delimiter 속성으로 지정한 구분자 문자가 추가된다.
    delimiter는 지정하지 않으면 defalut로 " "(space)가 적용된다.
	align은 정렬방식 이며 left, top, no 의 값을 지정한다.
	각각 왼쪽 정렬, 상단정렬, 정렬안함을 의미한다.
	align은 지정하지 않으면 default로 left 가 적용된다.
  -->
  <MergeElements delimiter=" " align="left">
    <ElementId>part1</ElementId>
    <ElementId>part2</ElementId>
    <ElementId>part3</ElementId>
      ...
    <ElementId>partN</ElementId>
  </MergeElements>
</Element>
```

사용방법 예시:

```xml
  <!-- 독립적으로 구성된 characterString element -->
  <Element id="part1" type="characterString">
  </Element>

  <Element id="part2" type="characterString">
  </Element>

  <Element id="part3" type="characterString">
  </Element>

  <!-- 위의 3개의 characterString element를 결합하는 element -->
  <Element id="part1-3" type="Merge">
        <Name>코드결합</Name>
    <MergeElements delimiter=" ">
      <ElementId>part1</ElementId>
      <ElementId>part2</ElementId>
      <ElementId>part3</ElementId>
    </MergeElements>
  </Element>

  <!-- 결합 element 필드 출력 -->
  <OutBlock id="merge">
    <Field id="1" name="코드결합">
      <ElementId>part1-3</ElementId>
    </Field>
  </OutBlock>
```

---

#### `Region element`

> TODO: 내용작성

---

#### `ContentMapping element`

string element의 내용을 다른텍스트로 맵핑한다.

결합하려는 element는 아래와 같은 조건을 만족해야 한다.

- 결합대상 element type: StaticText, CharacterString
- 결합대상 element의 결과 필드 개수: 1개

content mapping element 구성 및 사용방법 예시:

```xml
<Element id="content" type="ContentMapping">
  <Name>컨텐츠</Name>
  <!-- ContentMapInfo 태그에 맵핑정보를 기술한다.
    각 ContentMap 중에 가장 유사한 ContentMap의 Value로 매핑된다.
    유사도는 ContentKey의 정보로 유사도를 측정한다.
  -->
  <ContentMapInfo>
    <ContentMap>
      <!-- ContentKeyComplex 는 content key 정보를 포함한다.
        ContentKeyComplex 하위에는 ContentKey태그로 구성된다.
        ContentKey는 1개이상 나열이 가능하다.
        ContentKeyComplex 하위의 모든 ContnetKey가 만족되어야 매핑후보로 간주된다.
      -->
      <ContentKeyComplex>
        <!-- 각 ContnetKey는 지정된 하나의 엘리먼트의 내용과 유사도를 측정한다.
          아래의 예에서는 account_number와 bank_word 엘리먼트의 유사도를 측정을 수행한다.
          SearchText의 similarity는 최소유사도 기준이며 이 값을 넘었을 때만 매칭된 것으로 간주한다.
          만약 나열된 ContnetKey 중에 매칭실패가 하나라도 존재하면 매핑대상에서 제외된다.
          최종 ContentKeyComplex의 유사도는 각 ContentKey 유사도 중에 가장큰 값이 된다.
        -->
        <ContentKey elementId="account_number">
          <SearchText type="pattern" similarity="70">\d\d\d\d\d\d-\d\d-\d\d\d\d</SearchText>
        </ContentKey>
        <ContentKey elementId="bank_word">
          <SearchText type="text" similarity="70">국민</SearchText>
        </ContentKey>
      </ContentKeyComplex>
        <!-- ContentValue는 매핑될 텍스트 정보다. ContentMap 하위에 한번만 기술되어야 한다.
        -->
      <ContentValue>국민은행</ContentValue>
    </ContentMap>
    <ContentMap>
      <ContentKeyComplex>
        <ContentKey elementId="account_number">
          <!-- ContentKey 하위에는 SearchText를 1개 이상 나열할 수 있다.
              각 SearchText로 매칭을 시도하며 가장큰 유사도를 ContentKey의 유사도로 간주한다.
          -->
          <SearchText type="pattern" similarity="70">\d\d\d-\d\d\d\d-\d\d\d\d-</SearchText>
          <SearchText type="pattern" similarity="70">\d\d\d-\d\d-\d\d\d\d</SearchText>
        </ContentKey>
      </ContentKeyComplex>
      <ContentValue>농협은행</ContentValue>
    </ContentMap>
    <ContentMap>
      <ContentKeyComplex>
        <ContentKey elementId="account_number">
          <SearchText type="pattern" similarity="70">\d\d\d-\d\d\d\d\d\d-\d\d-\d</SearchText>
        </ContentKey>
      </ContentKeyComplex>
      <ContentValue>IBK기업은행</ContentValue>
    </ContentMap>
  </ContentMapInfo>
</Element>
```

#### `MachineLearning element`

딥러닝방식으로 필드영역을 탐색할 때 사용한다.
지정해야 할 정보는 다음과 같다.

- 대상 이미지 영역
- 딥러닝 수행 방식
- 딥러닝id와 서식id 매핑
- 필드 후처리 방식

```xml
<Element id="deep" type="MachineLearning">
  <Name>딥러닝 모델기반 필드</Name>

  <!-- 딥러닝 모델에 입력될 영역 설정
    maxFieldCount는 반드시 -1을 지정해서 여러개의 필드가 출력되도록 설정해 준다.
  -->
  <SearchRegionInfo maxFieldCount="-1">
    <SearchRegionComplex maxFieldCount="-1">
      <SearchRegionSet>
        <SearchRegion>
          <!-- 이미지에서 인식대상영역 설정
            이미지 크기대비 비율로 값을 지정 (0 ~ 100)
          -->
          <BasePosition type="imageOrigin"/>
          <Left unit="ImageSizeRatio">0</Left>
          <Top unit="ImageSizeRatio">0</Top>
          <Right unit="ImageSizeRatio">100</Right>
          <Bottom unit="ImageSizeRatio">100</Bottom>
        </SearchRegion>
      </SearchRegionSet>
    </SearchRegionComplex>
  </SearchRegionInfo>

  <!-- 딥러닝 수행방식 지정
    방법 1. 외부 프로그램 호출 방식
      engine: external
      execFilePath: 실행파일 경로
      argument: 인자
        - 매크로
          - $(SrcImagePath): 범용인식에 입력된 이미지 경로
          - $(ResultJsonPath): 필드정보가 저장된 json파일 경로.
            파일의 data 구조는 엔진에 정의된 형태로 구성되어야 함

    방법 2. 웹요청 방식(RestApi)
      engine: restApi
      url: 접근 주소
  -->
  <!--
  <Inference engine="external"
    execFilePath="../bin/deepField"
    argument="./form/form_01.pd $(SrcImagePath) $(ResultJsonPath)"
  />-->
  <Inference engine="restApi" url="$(ServerUrl)/boxing5"/>

  <!-- 필드 id 매핑
    모델의 id와 서식의 id가 다른 경우에는 반드시 id mapping을 해야 한다.
    두 id 가 동일한 경우에는 생략해도 된다.
    모든 id가 동일한 경우에는 <IdMapping> 전체를 생략가능하며
    일부 id만 다른 경우에는 해당 id에 대한 mapping 정보만 지정해 주면 된다.
  -->
  <IdMapping>
    <Mapping type="block" idOfModel="1" idOfForm="1"/> <!-- 생략 가능 -->
    <Mapping type="block" idOfModel="2" idOfForm="22"/> <!-- 모델id 2를 서식id 22로 매핑. 이 경우에는 반드시 내용을 추가해 줘야 한다. -->

    <Mapping type="field" idOfModel="1" idOfForm="1"/>
    <Mapping type="field" idOfModel="2" idOfForm="2"/>
    <Mapping type="field" idOfModel="3" idOfForm="3"/>
    <Mapping type="field" idOfModel="4" idOfForm="4"/>
  </IdMapping>

  <!-- 공통 필드 후처리 정보
    모든 필드에 공통으로 적용할 후처리 정보를 지정한다.
    지정가능한 정보는 다음과 같으며 PostProcessInfo와 ContentMapInfo는
    기존과 동일한 구조로 사용이 가능하다.
    - PostprocessInfo
      - ImageProcessInfo
      - RecogString
      - RemoveSpace
      - CorrectionInfo
    - ContentMapInfo
    - IMR
  -->
  <CommonPostprocess>
    <PostprocessInfo>
      <ImageProcessInfo>
        <ImageProcessDefine>
          <ImageProcess id="deep" type="machineLearning">
            <Parameters engine="external"
              execFilePath="../bin/deepImgProcess"
              argument="../model/denosing.pb $(SrcImagePath) $(ResultImagePath)"/>
          </ImageProcess>
        </ImageProcessDefine>
        <ImageProcessExec id="deep1" disabled="true" order="deep"/>
      </ImageProcessInfo>

      <RecogString disabled="true" ocrEngine="inzi" language="korean" charSet=""/>
      <RemoveSpace>false</RemoveSpace>

      <CorrectionInfo>
        <CorrectionDataSet>
          <CorrectionData>
            <SearchText type="regex">사댑장</SearchText>
            <NewText type="text">사업장</NewText>
          </CorrectionData>
        </CorrectionDataSet>
      </CorrectionInfo>
    </PostprocessInfo>

    <ContentMapInfo>
    </ContentMapInfo>

    <!-- IMR 필드 인식에 사용할 실행정보
        engine: external
        execFilePath: 실행파일 경로. ocrData 기준으로 상대경로 지정
        argument: 실행파일의 인자정보.
          현재(20201013) IMR 실행 인자는 입력이미지경로와 출력json 경로를 지정해 줘야 한다.
          각각 매크로 $(SrcImagePath)와 $(ResultJsonPath)을 사용해서 지정해 준다.
          매크로는 엔진내부에서 자동생성되는 파일경로를 지정할 때 사용된다.
    -->
    <IMR engine="external"
        execFilePath="../bin/IMRDetecter"
        argument="$(SrcImagePath) $(ResultJsonPath)"/>
  </CommonPostprocess>

  <!-- 필드별 후처리 정보
    각 필드별 특화된 후처리 정보를 지정한다.
    필드별 후처리 정보가 공통 후처리 정보보다 우선으로 적용된다.
    공통 후처리 정보만으로 처리가 되는 경우에는 생략이 가능하다.
    fieldType은 string일 때 생략이 가능하다.
  -->
  <FieldPostprocess idOfForm="3" fieldType="string" disabled="false">
    <PostprocessInfo>
      <RemoveSpace>true</RemoveSpace>
    </PostprocessInfo>
  </FieldPostprocess>

  <FieldPostprocess idOfForm="9" disabled="false">
    <PostprocessInfo>
      <CorrectionInfo>
        <CorrectionDataSet>
          <CorrectionData>
            <SearchText type="text" similarity="60">조회일</SearchText>
            <NewText type="text">조회일</NewText>
          </CorrectionData>
        </CorrectionDataSet>
      </CorrectionInfo>
    </PostprocessInfo>
  </FieldPostprocess>

  <!-- IMR 필드
    IMR 필드는 반드시 fieldType 속성을 imr로 명시해야 한다.
    IMR의 경우에는 IMR 실행정보를 IMR 태그로 지정해야 한다.
    각 필드별 지정도 가능하고 공통 후처리 정보에 지정하는 것도 가능하다.
  -->
  <FieldPostprocess idOfForm="10" fieldType="imr" disabled="false"/>
</Element>
```

`MachineLearning element`에 대한 outblock을 지정할 때는 아래와 같이 최종출력 필드id를 지정하고 element를 `MachineLearning element`의 id로 지정해 주면 된다.

```xml
  <!-- deep element에서 추출해주는 필드를 모두 나열해줌
      이때 field idNumber는 deep element의 idOfForm과 동일해야 한다.
  -->
  <OutBlock id="1">
    <Field idNumber="1" name="수신처" disabled="false">
      <ElementId>deep</ElementId>
    </Field>
  </OutBlock>
  <OutBlock id="2">
    <Field idNumber="2" name="문서번호" disabled="false">
      <ElementId>deep</ElementId>
    </Field>
  </OutBlock>
  <OutBlock id="3">
    <Field idNumber="3" name="요구일자" disabled="false">
      <ElementId>deep</ElementId>
    </Field>
    <Field idNumber="4" name="요구기관명" disabled="false">
      <ElementId>deep</ElementId>
    </Field>
  </OutBlock>

  <Element id="deep" type="MachineLearning">
    <!--
      추출필드 리스트
        - 수신처: idOfForm 1
        - 문서번호: idOfForm 2
        - 요구일자: idOfForm 3
        - 요구기관명: idOfForm 4
    -->
    ...
  </Element>
```

---

### `<SearchControl>`

탐색제어 정보를 정의한다. 설정하지 않아도 되며 (`SearchControl` 섹션이 없어도 됨) 이 때, 기본값은 `required` 이다.

```xml
<!-- 탐색 제어 -->
<SearchControl type="required">
    <FindIf type="DoNotFindIf">
        <ElementData/>
        ...
        <ElementData/>
    </FindIf>
</SearchControl>
```

- 속성
  `type`: element 탐색 제어 정보를 기술한다. 필수, 옵션, 금지 세가지 타입을 지정가능하다. 기본값은 `required` 이다. (이 속성은 서식식별 섹션 `FormIdentification` 에서만 적용된다.)

  - required: 필수, element가 반드시 존재해야 정상적인 탐색을 진행한다.
  - optional: 옵션, element의 존재유무에 상관없이 탐색을 진행한다.
  - prohibited: 금지, element가 반드시 없어야 정상적인 탐색을 진행한다.

---

#### `<FindIf>`

조건부 탐색제어 정보를 정의한다. 지정된 element들의 탐색결과에 따라 현재 element의 탐색을 제어할 수 있다.

```xml
<FindIf type="DoNotFindIf">
    <ElementData id="elemid_1" found="true"/>
    ...
    <ElementData id="elemid_n" found="false"/>
</FindIf>
```

- 속성
  `type`: `ElementData`에 기술된 element의 탐색유무 조건과 실제 탐색결과가 모두 일치하는 경우(참인 경우)에 현재 element를 탐색 할지 안 할지 지정한다.

  - FindIf: 조건이 모두 참이면 탐색한다.
  - DoNotFindIf: 조건이 모두 참이면 탐색하지 않는다.

- `<ElementData>`
특정 element의 탐색결과 조건을 지정한다.
  - `id`: element id
  - `found`: 탐색성공은 `true`, 그렇지 않으면 `false`를 지정한다.

- 예제 1
아래 예는 elemid_1, elemid_2, elemid_3 모두 탐색되었을 경우에만 현재 element를 탐색하지 않는다. 그렇지 않으면 탐색을 진행한다.

```xml
<FindIf type="DoNotFindIf">
    <ElementData id="elemid_1" found="true"/>
    <ElementData id="elemid_2" found="true"/>
    <ElementData id="elemid_3" found="true"/>
</FindIf>
```

- 예제 2
아래 예는 elemid_1, elemid_2, elemid_3 모두 탐색되었을 경우에만 현재 element를 탐색한다. 그렇지 않으면 탐색하지 않는다.

```xml
<FindIf type="FindIf">
    <ElementData id="elemid_1" found="true"/>
    <ElementData id="elemid_2" found="true"/>
    <ElementData id="elemid_3" found="true"/>
</FindIf>
```

- 예제 3
아래 예는 elemid_1는 탐색된 결과가 있고 elemid_2는 탐색된 결과가 없는 경우에만 현재 element를 탐색한다. 그렇지 않으면 탐색하지 않는다.

```xml
<FindIf type="FindIf">
    <ElementData id="elemid_1" found="true"/>
    <ElementData id="elemid_2" found="false"/>
</FindIf>
```

---

### `<SearchRegionInfo>`

탐색영역을 설정한다. 설정된 탐색영역이 없다면 이미지전체가 탐색영역으로 설정된다.

```xml
<!-- 탐색 영역 -->
<SearchRegionInfo maxFieldCount="1" priority="similarity">
    <SearchRegionComplex>
        <SearchRegionSet>
            <SearchRegion> ... </SearchRegion>
        </SearchRegionSet>
    </SearchRegionComplex>
</SearchRegionInfo>
```

- 속성

  `maxFieldCount`: 검사영역에서 탐색할 필드 개수 정의. 기본값은 `1` 이다. 모든 `SearchRegionComplex`의 결과에서 지정된 개수의 필드만 추출한다.

  - -1: 가능한 모든 필드를 추출
  - 0: 추출하지 않음
  - 1 ~ : 지정한 숫자만큼 필드를 추출. 필드는 priority 속성값 기준으로 우선순위를 적용하여 추출한다.

  `priority`: 추출할 필드의 우선순위. 기본값은 `similarity` 이다.

  - similarity: 유사도가 가장 높은 필드를 선택
  - nearestLeft: 검사영역 내에서 왼쪽에 가장 가까운 필드를 선택
  - nearestTop: 검사영역 내에서 상단에 가장 가까운 필드를 선택
  - nearestRight: 검사영역 내에서 오른쪽에 가장 가까운 필드를 선택
  - nearestBottom: 검사영역 내에서 하단에 가장 가까운 필드를 선택
  - nearestPosition: 지정한 위치에 가장 가까운 필드를 선택

  `x`, `y`: priority가 nearestPosition 일 때 위치값을 지정한다. 기본값은 x는 0, y는 0이다.

---

#### `<SearchRegionComplex>`

element를 탐색하기 위한 영역을 설정한다.

```xml
<SearchRegionComplex maxFieldCount="1" priority="similarity">
  <SearchRegionSet>
    <SearchRegion>
      <BasePosition type="imageOrigin"/> <!-- imageOrigin, formUnitRegionOrigin, inElement -->
      <Left unit="ImageSizeRatio">20</Left>
      <Top unit="ImageSizeRatio">0</Top>
      <Right unit="ImageSizeRatio">70</Right>
      <Bottom unit="ImageSizeRatio">30</Bottom>
    </SearchRegion>
  </SearchRegionSet>
</SearchRegionComplex>
```

- 속성

  `maxFieldCount`: 검사영역에서 탐색할 필드 개수 정의. 기본값은 `1` 이다.

  - -1: 가능한 모든 필드를 추출
  - 0: 추출하지 않음
  - 1 ~ : 지정한 숫자만큼 필드를 추출. 필드는 priority 속성값 기준으로 우선순위를 적용하여 추출한다.

  `priority`: 추출할 필드의 우선순위. 기본값은 `similarity` 이다.

  - similarity: 유사도가 가장 높은 필드를 선택
  - nearestLeft: 검사영역 내에서 왼쪽에 가장 가까운 필드를 선택
  - nearestTop: 검사영역 내에서 상단에 가장 가까운 필드를 선택
  - nearestRight: 검사영역 내에서 오른쪽에 가장 가까운 필드를 선택
  - nearestBottom: 검사영역 내에서 하단에 가장 가까운 필드를 선택
  - nearestPosition: 지정한 위치에 가장 가까운 필드를 선택

  `x`, `y`: priority가 nearestPosition 일 때 위치값을 지정한다. 기본값은 x는 0, y는 0이다.

- 하위태그

  `<SearchRegionSet>`: 탐색영역 세트를 지정. 하위에 `<SearchRegion>`를 필요한 만큼 정의할 수 있다. 두 개 이상의 `<SearchRegion>` 을 지정한 경우 'and' 개념이 적용되며 교집합 부분만 탐색영역으로 설정된다.

---

#### `<SearchRegion>`

element를 탐색하기 위한 영역을 설정한다. 기준에 따라서 세가지 설정방법이 있으며 `<BasePosition>`의 `type`속성으로 구분한다. 각 기준에 따라서 left, top, right, bottom 위치를 지정하면 된다.

- **공통기준 사용방식 (`<BasePosition>`을 사용하는 방식)**

  `<BasePosition>`을 사용하면 left, top, right, bottom 에 대해서 동일한 기준을 적용할 수 있다.

  - 이미지 기준 영역설정

    ```xml
    <SearchRegion>
      <BasePosition type="imageOrigin"/>
      <Left unit="ImageSizeRatio">0</Left>
      <Top unit="ImageSizeRatio">0</Top>
      <Right unit="ImageSizeRatio">100</Right>
      <Bottom unit="ImageSizeRatio">20</Bottom>
    </SearchRegion>
    ```

    `<BasePosition>`의 `type`을 "imageOrigin"으로 지정한다. 4개 위치의 `unit` 속성은 "ImageSizeRatio"로 지정해야 한다. 이미지의 가로와 세로 크기를 각각 100으로 보고 지정된 숫자만큼의 비율을 사용해서 영역을 구성한다.

  - form unit 영역 기준으로 영역설정

    ```xml
    <SearchRegion>
      <BasePosition type="formUnitRegionOrigin"/>
      <Left unit="unitSizeRatio">0</Left>
      <Top unit="unitSizeRatio">0</Top>
      <Right unit="unitSizeRatio">100</Right>
      <Bottom unit="unitSizeRatio">20</Bottom>
    </SearchRegion>
    ```

    `<BasePosition>`의 `type`을 "formUnitRegionOrigin"으로 지정한다. 4개 위치의 `unit` 속성은 "unitSizeRatio"로 지정해야 한다. formunit의 가로와 세로 크기를 각각 100으로 보고 지정된 숫자만큼의 비율을 사용해서 영역을 구성한다. form unit내부의 element에서 사용한다.

  - 다른 element 기준으로 영역설정

    ```xml
    <SearchRegion>
      <BasePosition type="inElement" baseElementId="elemId1" x="100" y="0"/>
      <Left unit="elementAvgCharHeightRatio">100</Left>
      <Top unit="elementRegionHeightRatio">-50</Top>
      <Right unit="elementAvgCharHeightRatio">3000</Right>
      <Bottom unit="elementRegionHeightRatio">150</Bottom>
    </SearchRegion>
    ```

    `<BasePosition>`의 `type`을 "inElement"로 지정한 후 반드시 `baseElementId`, `x`, `y` 속성을 지정해야 한다.

    - `baseElementId`: 기준 element의 id

    - `x`, `y`: 기준 element의 가로폭, 높이를 각각 100으로 봤을 때 기준점 위치(비율, 0 ~ 100)

    - `unit`: 거리 단위

      - pixel: 픽셀
      - elementAvgCharHeightRatio: 별도 지정한 엘리먼트의 평균 문자 높이 비율
      - elementRegionHeightRatio: 별도 지정한 엘리먼트의 높이 비율
      - elementRegionWidthRatio: 별도 지정한 엘리먼트의 폭 비율

- **독립기준 사용방식**

  `<BasePosition>`을 사용하지 않고 각 left, top, right, bottom 의 기준을 독립적으로 적용할 수 있다. left, top, right, bottom 별로 `<BasePosition>`에서 사용했던 속성을 그대로 적용이 가능하다. 단 `type` 속성은 `basePositionType`을 사용해야 한다.

  ```xml
  <SearchRegion>
    <Left basePositionType="inElement" baseElementId="elemId1" x="100" y="0" unit="elementAvgCharHeightRatio">100</Left>
    <Top basePositionType="inElement" baseElementId="elemId2" x="100" y="100" unit="elementRegionHeightRatio">-50</Top>
    <Right basePositionType="imageOrigin" unit="ImageSizeRatio">300</Right>
    <Bottom basePositionType="inElement" baseElementId="elemId3" x="0" y="100" unit="elementRegionHeightRatio">150</Bottom>
  </SearchRegion>
  ```

  위의 코드에서 각 위치별 기준은 아래와 같다.

  - left: elemId1 의 x, y 각 100, 0을 기준
  - top: elemId2 의 x, y 각 100, 100을 기준
  - right: 이미지 기준
  - bottom: elemId3의 x, y 각 0, 100을 기준

- **혼용기준 사용방식**

  공통기준과 독립기준을 혼용으로 사용이 가능하다. 아래 코드와 같이 `<BasePosition>`으로 공통기준을 설정하고 독립기준을 사용하고자 하는 부분에만 독립기준방식을 적용하는 것이 가능하다. 아래코드는 left, top, bottom은 공통기준, right는 독립기준을 적용하는 예이다.

  ```xml
  <SearchRegion>
    <BasePosition type="inElement" baseElementId="elemId1" x="100" y="0"/>
    <Left unit="elementAvgCharHeightRatio">100</Left>
    <Top unit="elementRegionHeightRatio">-50</Top>
    <Right basePositionType="imageOrigin" unit="ImageSizeRatio">300</Right>
    <Bottom unit="elementRegionHeightRatio">150</Bottom>
  </SearchRegion>
  ```

---

### `<PreprocessInfo>`

``` xml
<!-- 탐색을 위한 전처리
  좌우텍스트 영역 결합, 텍스트 교정
  -->
<PreprocessInfo>
    <ImageProcessInfo/>
    <ExtractTextRegion/>
    <RecogString/>
    <MergeTextRegionOnALine>false</MergeTextRegionOnALine>
    <CorrectionInfo/>
</PreprocessInfo>
```

---

#### `<ImageProcessInfo>`

현재 element 탐색영역의 이미지에 대해서 이미지전처리 수행 옵션을 지정한다.

``` xml
<ImageProcessInfo>
    <ImageProcessDefine>
        <!-- 이미지 전처리 옵션 정의 -->
        <ImageProcess id="bin1" type="binarize">
            <Parameters method="MultiWindow" kRatio="" kOffset="0.2"/>
        </ImageProcess>
    </ImageProcessDefine>

    <!-- 이미지 전처리 수행 제어 -->
    <ImageProcessExec id="bin1" disabled="false" order="bin1"/>
</ImageProcessInfo>
```

이미지전처리 수행옵션은 아래와 같이 두 종류 정보를 설정해야 한다.

- `<ImageProcessDefine>`: 수행할 이미지전처리 옵션을 정의하는 부분

  하위에 `<ImageProcess>` 태그를 사용해서 1개 이상의 이미지전처리 옵션을 설정할 수 있다. `<ImageProcess>`는 반드시 `id`, `type` 속성을 지정해야 한다. `id`는 `<ImageProcessExec>` 에서 참조하기 위해서 사용하며 `type`은 이미지전처리의 종류를 지정하기 위해서 사용한다.

  ``` xml
  <ImageProcessDefine>
      <!-- 필요한 만큼 이미지전처리 정보를 등록 가능 -->
      <ImageProcess id="" type=""/>
      ...
      <ImageProcess id="" type=""/>
  </ImageProcessDefine>
  ```

- `<ImageProcessExec>`: 정의된 이미지전처리 수행을 제어하는 부분

  1개 이상 옵션을 설정할 수 있다. `order` 속성에 수행할 전처리의 id를 지정한다. 2개 이상의 이미지전처리를 연속으로 수행할 수 있으며 `order`에 수행할 전처리 순서대로 ';' 로 구분해서 id를 지정해 준다. `id`를 지정하면 텍스트 로그에 출력된다. `disabled`는 전처리 수행 유무를 지정한다. 이미지전처리 결과 이미지는 로그디렉토리에 이미지파일로 저장된다.

  ``` xml
  <ImageProcessInfo>
      <ImageProcessDefine>
          <!-- 이진화 -->
          <ImageProcess id="bin1" type="binarize">
              <Parameters method="MultiWindow" kRatio="" kOffset="0.2"/>
          </ImageProcess>
          <!-- 역상 -->
          <ImageProcess id="inv1" type="inverse"/>
      </ImageProcessDefine>

      <!-- 아래는 3종류의 이미지전처리를 수행한 이미지를 생성한다.
          각 이미지에 대해서 필드추출을 시도하게 되며 similarity가 가장 높은 결과를 최종 선택한다.
      >
      <!-- 이진화 -> 역상 -->
      <ImageProcessExec id="bin1" disabled="false" order="bin1;inv1"/>
      <!-- 역상 -> 이미지 -->
      <ImageProcessExec id="bin2" disabled="false" order="inv1;bin1"/>
      <!-- 역상 -->
      <ImageProcessExec id="inv1" disabled="false" order="inv1"/>
  </ImageProcessInfo>
  ```

사용가능한 이미지전처리는 아래와 같다.

- 이진화(bizarize)

  `type`은 _binarize_ 로 지정한다. 이진화 알고리즘은 local threshold 기반의 MultiWindows, ModifiedNiblack 두가지를 사용할 수 있다.

  - MultiWindows

    텍스트문서 이미지 이진화에 최적화된 이진화 알고리즘이다. local threshold를 계산할 때 크기가 다른 두개의 window를 사용해서 문자 부분의 이진화를 개선했다. 아래의 코드와 같은 개념으로 local threshold를 계산한다.

    `thread = mean + k * standard_deviation`

    속성에는 이 `k`와 관련된 정보를 설정할 수 있다.

    - k: 유효값은 0.0 ~ 1.0 이다. 지정하지 않으면 엔진내부에서 이미지분석을 통해 최적의 값을 찾아서 `k`를 자동지정한다. `kRatio`와 `kOffset` 으로 미세조정할 수 있다. 미세조정 수식은 아래와 같다.

      `k = k*kRatio/100 + kOffset`

    - kRatio: 유효값은 0 ~ 100 이다. 기본값은 _100_ 이다.
    - kOffset: 유효값은 0.0 ~ 1.0 이다. 기본값은 _0.0_ 이다.

    ``` xml
    <ImageProcess id="bin1" type="binarize">
        <Parameters method="MultiWindow" k="" kRatio="" kOffset=""/>
    </ImageProcess>
    ```

  - ModifiedNiblack

    Niblack 알고리즘을 개선한 알고리즘이다. `k`, `kRatio`, `kOffset`을 속성으로 지정할 수 있으며 MultiWindows와 사용방법은 동일하다.

    ``` xml
    <ImageProcess id="bin1" type="binarize">
        <Parameters method="ModifiedNiblack" k="" kRatio="" kOffset=""/>
    </ImageProcess>
    ```

- 역상(inverse)

  역상 정보는 아래와 같이 설정한다. `type`속성은 _inverse_ 로 지정한다.

  ``` xml
  <ImageProcess id="inv1" type="inverse"/>
  ```

- 노이즈 제거 (denoise)

  `type`은 _denoise_ 로 지정한다. 사용가능한 이미지노이즈 제거 알고리즘은 아래와 같다.

  - median filter

    이미지의 salt-pepper type noise 제거를 수행
    - windowSize: 값이 클수록 배경 제거 효과가 커진다. (기본값: _3_ )

    ``` xml
    <ImageProcess id="" type="denoise">
        <Parameters method="medianFilter" windowSize="3"/>
    </ImageProcess>
    ```

  - adaptive median filter

    이미지의 salt-pepper type noise 제거를 수행
    - windowSize: 값이 클수록 배경 제거 효과가 커진다. (기본값: _3_ )

    ``` xml
    <ImageProcess id="" type="denoise">
        <Parameters method="adaptiveMedianFilter" windowSize="3"/>
    </ImageProcess>
    ```

  - background cleaner

    이미지의 배경 패턴(lines, dot, etc) 제거
    - windowSize: 값이 클수록 배경 제거 효과가 커진다. (기본값: _3_ )
    - edgeThresh: 배경 제거 시, 이미지의 디테일을 얼마나 남길지를 좌우한다. 값이 클수록 배경 제거 효과가 커진다. (기본값: _100_ )
    - pixelDiffThresh: 중심 픽셀과 주변 픽셀과의 차이 threshold. 값이 클수록 배경 노이즈 제거 효과가 커진다. (기본값: _20_ )

    ``` xml
    <ImageProcess id="" type="denoise">
        <Parameters method="backgroundCleaner" windowSize="3" edgeThresh="100" pixelDiffThresh="20"/>
    </ImageProcess>
    ```

  - despeckle

    이미지의 작은 반점 제거. 최대허용크기보다 작은 반점을 모두 제거한다.
    - speckleWidth: 반점의 최대 가로 크기 (pixel) (기본값: _3_)
    - speckleHeight: 반점의 최대 세로 크기 (pixel) (기본값: _3_)

    ``` xml
    <ImageProcess id="" type="denoise">
        <Parameters method="despeckle" speckleWidth="5" speckleHeight="5"/>
    </ImageProcess>
    ```

  - adaptive mean filter

    이미지 노이즈 (additive white Gaussian noise) 제거
    - windowSize: 값이 클수록 배경 제거 효과가 커진다. (기본값: _3_ )
    - noiseVariance: [25, 400]의 값을 사용하는 것을 추천. 유효 값: (0, 16384) (기본값: _100_ )

    ``` xml
    <ImageProcess id="" type="denoise">
        <Parameters method="adaptiveMeanFilter" windowSize="5" noiseVariance="100"/>
    </ImageProcess>
    ```

  - LLMMSE filter

    이미지 노이즈 (additive white Gaussian noise) 제거
    - windowSize: 값이 클수록 배경 제거 효과가 커진다. (기본값: _3_ )
    - noiseVariance: [25, 400]의 값을 사용하는 것을 추천. 유효 값: (0, 16384) (기본값: _100_ )

    ``` xml
    <ImageProcess id="" type="denoise">
        <Parameters method="LLMMSEFilter" windowSize="5" noiseVariance="100"/>
    </ImageProcess>
    ```

  - directional wiener filter

    이미지 노이즈 (additive white Gaussian noise) 제거
    - windowSize: 값이 클수록 배경 제거 효과가 커진다. (기본값: _3_ )
    - noiseVariance: [25, 400]의 값을 사용하는 것을 추천. 유효 값: (0, 16384) (기본값: _100_ )

    ``` xml
    <ImageProcess id="" type="denoise">
        <Parameters method="directionalWienerFilter" windowSize="5" noiseVariance="100"/>
    </ImageProcess>
    ```

  - sigma filter

    이미지 노이즈 (additive white Gaussian noise) 제거
    - windowSize: 값이 클수록 배경 제거 효과가 커진다. (기본값: _3_ )
    - noiseVariance: [25, 400]의 값을 사용하는 것을 추천. 유효 값: (0, 16384) (기본값: _100_ )

    ``` xml
    <ImageProcess id="" type="denoise">
        <Parameters method="sigmaFilter" windowSize="5" noiseVariance="100"/>
    </ImageProcess>
    ```

- 리사이즈
  가로와 세로의 리사이즈 비율을(0 ~ 100) widthRatio, heightRatio 속성으로 지정한다. 100이 원본이미지와 같은 비율을 의미한다.
  ``` xml
  <ImageProcess id="" type="resize">
      <Parameters widthRatio="50" heightRatio="50"/>
  </ImageProcess>
  ```

- 회전
  angle 값을 -360 ~ 360(양수는 반시계방향, 음수는 시계방향) 로 지정한다. (예: 90 이면 왼쪽 90도로 회전시킴)
  ``` xml
  <ImageProcess id="" type="rotate">
      <Parameters angle="90"/>
  </ImageProcess>
  ```

- 머신러닝기반 전처리(machineLearning)

  `type`은 _machineLearning_ 로 지정한다.

  - external

    외부 딥러닝 실행파일을 수행해서 전처리결과를 가져온다.

    - execFilePath: 실행파일의 경로. ocrData 기준 상대경로로 지정해 준다.
    - argument: 명령인자. 명령인자에는 정의된 매크로를 사용할 수 있다.
      - $(SrcImagePath): 현재 전처리하려는 이미지파일의 경로. 엔진내부에서 자동으로 생성된 경로를 반환해 준다.
      - $(ResultImagePath): 전처리 결과 이미지 경로. 엔진내부에서 자동으로 생성된 경로를 반환해 준다. 이 경로의 이미지를 자동으로 로드한다.

    ``` xml
    <ImageProcess id="" type="machineLearning">
        <Parameters engine="external" execFilePath="../bin/deepImgProcess" argument="../model/denosing.pb $(SrcImagePath) $(ResultImagePath)"/>
    </ImageProcess>
    ```

---

#### `<ExtractTextRegion>`

현재 element의 텍스트영역 추출 옵션을 지정한다. 옵션을 지정하지 않거나  `disabled`가 true이면 엔진 공통 옵션을 사용한다.

```xml
<ExtractTextRegion disabled="false" documentType="document" subOption="table" reduceRatio16="16" inverseType="0"/>
```

- 속성

  `disabled`: 옵션사용유무

  - true: 사용하지 않는다.
  - false: 사용한다.

  `documentType`: 문서 타입

- document: 일반문서에 적합한 방법을 사용해서 텍스트 영역 추출
  - bizcard: 명함에 적합한 방법을 사용해서 텍스트 영역 추출
  - single_column: 1단 문서전용 텍스트 영역 추출

  `subOption`: 문서특성 옵션 (세부처리 옵션). 아래의 옵션을 ;로 구분해서 2개 이상 설정가능

  - 공통 옵션
    - no: 설정안함
    - partial_inverse: 부분역상된 부분의 텍스트라인 추출 강화
    - closed_line: 좁은 줄간격 텍스트라인 추출 강화
  - 일반문서 전용 옵션 (documentType값이 document 인 경우만 적용됨)
    - table: 표 내부의 텍스트라인 추출 강화 (default)
    - 2d_barcode: 2D바코드 추출방지 (등본 인터넷발급본에 존재하는 형태).
    - word_output: word 형태로 텍스트라인을 추출
    - ruling_line: 괘선 추출
  - 명함 전용 옵션 (documentType값이 bizcard 인 경우만 적용됨)
    - cropped: 크롭된 명함

  `reduceRation16`:축소 비율 (0 ~ 16). 텍스트라인 모듈 독자적으로 축소에 사용할 비율

  - 0: 자동 축소비율 계산 (default)
  - 1 ~ 15: n/16 한 값으로 이미지를 축소 후 텍스트라인추출 수행
  - 16: 축소없이 원본이미지 사용

  `inverseType`: 역상처리 방식

  - 0: 자동으로 역상판단해서 처리 (default)
  - 1: 역상처리 안 함
  - 2: 역상처리 함

---

#### `<RecogString>` in preprocess

현재 element의 문자열 인식 옵션을 지정한다. 옵션을 지정하지 않거나  `disabled`가 true이면 엔진 공통 옵션을 사용한다.

```xml
<RecogString disabled="true" ocrEngine="inzi" language="korean" charSet="" fullTextRecognition="false" clovaocrUrl="" clovaocrSecretKey=""/>
```

- 속성

  - `disabled`: 옵션사용 유무
    - true: 사용하지 않는다.
    - false: 사용한다.
  - `ocrEngine`: ocr 엔진 선택
    - inzi: 인지소프트 ocr 엔진
    - tess: tesseract 엔진
    - clova_ocr: 네이버 클라우드 플랫폼 clova ocr 엔진
  - `language`: 인식할 언어. 언어에 맞는 인식모델을 사용해서 인식한다.
    - korean: 한국어
    - latin: 라틴어
  - `charSet`: 인식할 문자 정보. 지정한 문자로만 인식한다. 두개 이상 지정가능하며 ';' 문자로 구분해 준다. (예: Symbol;Digit )
    - Symbol: Ascii 범위내 기호
    - Digit: 숫자
    - Alphabet: 영어알파벳 대소문자
    - Alphabet-lower: 영어알파벳 소문자
    - Alphabet-upper: 영어알파벳 대문자
    - Hangul: 한글
    - Hanja: 한자
    - AllChar: 모든 문자
  - `fullTextRecognition`: 모든 text를 미리 인식할 지 아니면 필요한 부분만 인식하도록 할지 지정해 준다. 기본값은 false 다.
    - true: 모든 text를 무조건 인식
    - false: 필요한 부분의 text만 인식
  - `clovaocrUrl`, `clovaocrSecretKey`: `ocrEngine` 이 clova_ocr 인 경우에 필요하다. 네이버클라우드 플랫폼 clova ocr에서 발급받은 url과 key를 지정해 준다.

---

#### `<MergeTextRegionOnALine>`

true이면 탐색영역 내의 텍스트 라인 중에서 하나의 텍스트 라인으로 볼 수 있는 것 들을 결합해 준다. `maxDistanceRatioToFieldHeight` 속성으로 결합될 거리의 최대치를 지정할 수 있다. 지정하지 않으면 기본값은 500이다.

``` xml
<MergeTextRegionOnALine maxDistanceRatioToFieldHeight="100">true</MergeTextRegionOnALine>
```

---

#### `<CorrectionInfo>` in preprocess

탐색대상 텍스트를 교정한다.

[`<CorrectionInfo>`](#`<CorrectionInfo>`) 참고

---

### `<SearchFieldInfo>`

``` xml
<!-- 탐색 필드 정보 -->
<SearchFieldInfo>
    <SearchTextSet>
        <SearchText></SearchText>
        <SearchText></SearchText>
    </SearchTextSet>
    <SearchTextListFile/>
    <StringLengthRange/>
    <RowCountRange/>
    <CharSize>
        <Height unit="imageHeightRatio" from="0" to="1.5"/>
    </CharSize>
    <PermitMultipleLines>false</PermitMultipleLines>
    <TakeSpacesIntoAccount>false</TakeSpacesIntoAccount>
</SearchFieldInfo>
```

---

#### `<SearchText>`

탐색할 텍스트의 정보를 지정한다.

```xml
<SearchText type="text" similarity="90">주소</SearchText>
```

탐색할 텍스트는 한 개이상의 문자를 지정해야 한다. 지정하지 않으면 에러를 발생시킨다.

- 특수문자 탐색

  탐색할 텍스트에 포함된 xml 특수문자("&'<>)는 아래의 변환표를 참조해서 10진수 또는 이스케이프 문자로 변환해 줘야 한다.
  | 원래문자 | 10진수  | 이스케이프 문자 |
  | ------- | ------- | -------------- |
  | "       | `&#34;` | `&quot;`       |
  | &       | `&#38;` | `&amp;`        |
  | '       | `&#39;` | `&apos;`       |
  | <       | `&#60;` | `&lt;`         |
  | >       | `&#62;` | `&gt;`         |

  만약 공백(space)을 단독으로 사용하려면 `&#32;`를 사용해야한다. 다른 문자와 함께 사용하는 공백은 변환하지 않아도 된다.

  ```xml
  <!-- 공백을 찾는다. -->
  <SearchText type="regex">&#32;</SearchText>

  <!-- 변환되지 않는 공백문자는 에러를 발생한다. 아래 구문은 사용불가!!! -->
  <SearchText type="regex"> </SearchText>

  <!-- 문자와 함께 사용하는 공백은 그대로 사용이 가능하다.
   " 우리 나라 "으로 입력된다. 문자열 시작과 끝에 있는 공백이 살아 있는 것에 유의해라.
   문자열 끝 부분의 공백은 실수를 유발할 수 있으므로 사용하지 않는 것을 권장한다. -->
  <SearchText type="regex"> 우리 나라 </SearchText>
  <SearchText type="regex">우리 나라</SearchText> <!-- 권장 -->
  ```

- 속성

  - `type`: 텍스트 탐색방식을 지정한다.

    - _text_: 문자열 매칭 방식([_Approximate string matching_](https://en.wikipedia.org/wiki/Approximate_string_matching))을 사용한다. 두 문자열의 유사도를 계산해서 `similarity`보다 크면 매칭된 것으로 간주한다. `similarity`는  _0 ~ 100_ 사이의 값을 지정할 수 있다. 만약 `similarity`를 생략하면 기본값 _100_ 으로 설정된다.  매칭할 때 ___공백은 무시___ 되며 ___대소문자 구분___ 을 하지 않는다.

      ```xml
      <!-- "주소"와 유사도 90 이상인 텍스트를 찾는다. -->
      <SearchText type="text" similarity="90">주소</SearchText>

      <!-- 공백은 무시되기 때문에 "주소"와 같다. -->
      <SearchText type="text" similarity="90">주 소</SearchText>

      <!-- similarity가 없으면 기본값 100으로 설정된다. 아래 두 구문은 같다. -->
      <SearchText type="text">주소</SearchText>
      <SearchText type="text" similarity="100">주소</SearchText>
      ```

    - _regex_: [정규식](https://ko.wikipedia.org/wiki/정규_표현식)을 사용한다. `similarity` 속성은 지정하지 않는다.

      ```xml
      <!-- 경기도, 겅기도, 깅기도 를 찾는다. -->
      <SearchText type="regex">[경겅깅]기도</SearchText>
      ```

    - _pattern_: 자리수가 고정된 간단한 형태의 패턴을 사용한다. 사용가능한 _pattern meta_ 문자는 아래와 같다.
      - _\d_: 숫자
      - _\h_: 한글
      - _\j_: 한자
      - _\a_: 알파벳 대소문자
      - _\l_: 알파벳 소문자
      - _\u_: 알파벳 대문자
      - _\w_: 모든문자 (숫자, 한글, 한자, 알파벳 대소문자)
      - _[]_: []안의 문자중 하나

      ```xml
      <!-- 주민번호 패턴 -->
      <SearchText type="pattern" similarity="70">\d\d[01]\d[0-3]\d-[1-4]\d\d\d\d\d\d</SearchText>

      <!-- 자동차번호 패턴 -->
      <SearchText type="pattern" similarity="70">\d\d\h\d\d\d\d</SearchText>

      <!-- 날짜 패턴 -->
      <SearchText type="pattern" similarity="70">\d\d\d\d년 \d\d월 \d\d일</SearchText>
      <SearchText type="pattern" similarity="70">\d\d\d\d-\d\d-\d\d</SearchText>
      ```

    - _charSet_: 문자구성 정보를 사용한다. 지정한 문자의 비율을 유사도로 사용한다. 아래의 미리정의된 문자세트를 지정할 수도 있다.
      - _Symbol_: Ascii 범위내 기호
      - _Digit_: 숫자
      - _Alphabet_: 영어알파벳 대소문자
      - _Alphabet-lower_: 영어알파벳 소문자
      - _Alphabet-upper_: 영어알파벳 대문자
      - _Hangul_: 한글
      - _Hanja_: 한자
      - _AllChar_: 모든 문자

      ';'(세미콜론)으로 group을 구분한다. 문자범위는 '-'(하이픈)으로 표기한다. '-'(하이픈)을 문자세트로 지정하려면 '--'(두개연속)으로 표기한다.

      ```xml
      <!-- 숫자. 아래의 두 방식이 모두 가능하다. -->
      <SearchText type="charSet" similarity="70">Digit</SearchText>
      <SearchText type="charSet" similarity="70">0-9</SearchText>

      <!-- 숫자와 기호 -->
      <SearchText type="charSet" similarity="70">Digit;Symbol</SearchText>

      <!-- 숫자, 한글, '(', ')', '-'. '-'은 '--'로 지정해야 한다. -->
      <SearchText type="charSet" similarity="70">Digit;Hangul;()--</SearchText>

      <!-- 모든문자. 인식된 텍스트를 그대로 출력하고자 할 때 사용한다. -->
      <SearchText type="charSet" similarity="100">AllChar</SearchText>
      ```

  - `takeSpacesIntoAccount`: 공백을 고려한 탐색을 유무를 지정한다.
    - _true_: 공백을 고려한 탐색을 진행
    - _false_: 공백을 무시한 탐색을 진행 (default)

  - `similarity`: 탐색할 문자열의 유사도이다. 지정 가능한 값은 _0 ~ 100_ 이며 이 값보다 큰 경우에 매칭된 것으로 간주한다. `type`속성이 _text_, _pattern_, _charSet_ 인 경우에만 사용한다. 생략하면 기본값 _100_ 으로 설정된다.

---

#### `<SearchTextListFile>`

탐색문자열 리스트가 포함된 파일을 지정한다. 파일은 text 파일이어야 하며 utf8 encoding으로 작성되어야 한다. text 파일에는 탐색문자열이 줄바꿈으로 구분해서 저장되어야 한다. 파일을 지정할 경우에는 `<SearchText>` 정보를 지정하지 않는 것을 권장한다.

``` xml
<SearchTextListFile>searhTextList.txt</SearchTextListFile>
```

---

#### `<StringLengthRange>`

> TODO: 내용작성

문자열의 길이를 지정한다.

``` xml
<StringLengthRange from="" to=""/>
```

---

#### `<WordCountRange>`

추출할 텍스트의 단어 개수를 지정한다.

``` xml
<WordCountRange from="" to=""/>
```

(`from`, `to`)값과 추출가능한 단어의 개수

- (,): 단어개수 제한 없음
- (0, 0): 단어개수 제한 없음
- (2, 2): 단어가 2개인 텍스트만 추출
- (1, 3): 단어가 1 ~ 3 개인 텍스트만 추출
- (0, 3) or (, 3): 단어가 0 ~ 3개인 텍스트만 추출
- (2, 0) or (2, ): 단어가 최소 2개 이상인 텍스트만 추출

---

#### `<RowCountRange>`

추출할 텍스트 라인의 개수를 지정한다.

``` xml
<RowCountRange from="" to=""/>
```

(`from`, `to`)값과 추출가능한 텍스트 라인의 개수

- (,): 모든값을 지정하지 않으면 1개만 추출
- (0, 0): 모든 텍스트 라인을 추출
- (2, 2): 2개만 추출
- (1, 3): 1 ~ 3 개 추출
- (0, 3) or (, 3): 0 ~ 3개  추출
- (2, 0) or (2, ): 최소 2개 이상 텍스트 라인을 추출

---

#### `<CharSize>`

탐색할 문자의 크기를 설정한다. 설정된 문자의 크기 범위에 해당되는 텍스트만 탐색한다.

```xml
<CharSize>
    <Height unit="imageHeightRatio" from="0" to="1.5"/>
</CharSize>
```

`<Height>` 태그로 문자의 높이 범위를 설정할 수 있다. `from`과 `to` 속성에 범위를 입력할 수 있고 소숫점 숫자형태로 입력이 가능하다.

`unit`: 길이단위이며 입력가능한 값은 아래와 같다.

- imageHeightRatio: 이미지 높이 비율 기준
- imageWidthRatio: 이미지 폭 비율 기준
- pixel: 픽셀 기준

---

#### `<PermitMultipleLines>`

멀티라인 텍스트 추출 허용 유무를 설정한다. 멀티라인 텍스트는 아래 문서에서 `세대구성 사유 및 일자` 와 같이 텍스트가 길어서 줄바꿈 된 것을 의미한다.

![주민등록등본](figure_1.jpg "주민등록등본 이미지")

`true` 또는 `false`로 설정한다. 기본값은 `false`다.

- `true`: 멀티라인 텍스트 탐색을 시도한다.
- `false`: 단일라인 텍스트 탐색을 시도한다. (default)

위의 문서이미지에서 "세대구성 사유 및 일자" 텍스트를 찾으려면 `<PermitMultipleLines>`의 값을 `true`로 설정해야 탐색이 가능하다.

```xml
<SearchFieldInfo>
  <SearchTextSet>
    <SearchText type="text">세대구성 사유 및 일자</SearchText>
  </SearchTextSet>
  <PermitMultipleLines>true</PermitMultipleLines>
</SearchFieldInfo>
```

---

#### `<TakeSpacesIntoAccount>`

텍스트 탐색할 때 공백을 고려한 탐색을 진행할 지 설정한다. `true` 또는 `false`로 설정한다. 기본값은 `false`다. 적용범위는 `<SearchTextSet>` 의 모든 `<SearchText>`에 적용된다.

- `true`: 텍스트의 공백을 고려해서 탐색한다.
- `false`: 텍스트의 공백을 무시하고 탐색한다. (default)

예를 들어 "안 녕 하 세 요" 텍스트를 탐색할 때 아래 코드는 탐색에 실패한다.

```xml
<!-- "안 녕 하 세 요" 텍스트 탐색 실패 코드 -->
<SearchFieldInfo>
  <SearchTextSet>
    <SearchText type="text">안녕하세요</SearchText>
  </SearchTextSet>
  <TakeSpacesIntoAccount>true</TakeSpacesIntoAccount>
</SearchFieldInfo>
```

아래 코드와 같이 `TakeSpacesIntoAccount`의 값을 `false`로 설정해야 공백을 무시하고 탐색이 가능하다.

```xml
<!-- "안 녕 하 세 요" 텍스트 탐색 가능 -->
<SearchFieldInfo>
  <SearchTextSet>
    <SearchText type="text">안녕하세요</SearchText>
  </SearchTextSet>
  <TakeSpacesIntoAccount>false</TakeSpacesIntoAccount>
</SearchFieldInfo>
```

아래 코드와 같이 `TakeSpacesIntoAccount`을 설정하지 않으면 기본값 `false`가 적용되어 공백무시 탐색을 수행한다.

```xml
<!-- "안 녕 하 세 요" 텍스트 탐색 가능 -->
<SearchFieldInfo>
  <SearchTextSet>
    <SearchText type="text">안녕하세요</SearchText>
  </SearchTextSet>
</SearchFieldInfo>
```

`<SearchText>`의 `takeSpacesIntoAccount` 속성과 중복설정되는 경우에는 `<SearchText>`의 값을 우선적용한다.

```xml
<SearchFieldInfo>
  <SearchTextSet>
    <SearchText type="text" takeSpacesIntoAccount="false">안녕하세요</SearchText> <!-- false: 속성의 값이 우선 적용 -->
    <SearchText type="text">반갑습니다</SearchText> <!-- true: <TakeSpacesIntoAccount>의 값이 적용 -->
  </SearchTextSet>
  <TakeSpacesIntoAccount>true</TakeSpacesIntoAccount>
</SearchFieldInfo>
```

---

### `<PostprocessInfo>`

필드 후처리 정보를 설정한다.

``` xml
<PostprocessInfo>
    <!-- 후처리
      재인식, 텍스트 교정, 스페이스 제거, 보안처리, 영역 조정
    -->
    <RecogString/>
    <CorrectionInfo></CorrectionInfo>
    <RemoveSpace></RemoveSpace>
    <Security></Security>
    <AdjustFieldRegion></AdjustFieldRegion>
    <MergeAllFields></MergeAllFields>
</PostprocessInfo>
```

---

#### `<RecogString>` in postprocess

추출된 필드를 재인식한다. `language`와 `charSet` 정보를 사용해서 문자열인식을 수행한다.

```xml
<RecogString disabled="true" ocrEngine="inzi" language="korean" charSet=""/>
```

- `disabled`: 재인식 수행 유무 지정
  - true: 수행하지 않는다.
  - false: 수행한다.
- `ocrEngine`: ocr 엔진 선택
  - inzi: 인지소프트 ocr 엔진
  - tess: tesseract 엔진
  - clova_ocr: 네이버 클라우드 플랫폼 clova ocr 엔진
- `language`: 인식할 언어. 언어에 맞는 인식모델을 사용해서 인식한다.
  - korean: 한국어
  - latin: 라틴어
- `charSet`: 인식할 문자 정보. 지정한 문자로만 인식한다. 두개 이상 지정가능하며 ';' 문자로 구분해 준다. (예: Symbol;Digit )
  - Symbol: Ascii 범위내 기호
  - Digit: 숫자
  - Alphabet: 영어알파벳 대소문자
  - Alphabet-lower: 영어알파벳 소문자
  - Alphabet-upper: 영어알파벳 대문자
  - Hangul: 한글
  - Hanja: 한자
  - AllChar: 모든 문자
- `fullTextRecognition`: 모든 text를 미리 인식할 지 아니면 필요한 부분만 인식하도록 할지 지정해 준다. 기본값은 false 다.
  - true: 모든 text를 무조건 인식
  - false: 필요한 부분의 text만 인식
- `clovaocrUrl`, `clovaocrSecretKey`: `ocrEngine` 이 clova_ocr 인 경우에 필요하다. 네이버클라우드 플랫폼 clova ocr에서 발급받은 url과 key를 지정해 준다.

---

#### `<CorrectionInfo>` in postprocess

추출된 필드의 텍스트를 교정한다.

[교정 정보 참고](#`<CorrectionInfo>`)

---

#### `<RemoveSpace>`

``` xml
<RemoveSpace>true</RemoveSpace>
```

true이면 추출된 필드의 텍스트에서 공백을 제거해 준다.

---

#### `<AdjustFieldRegion>`

> TODO: 내용작성

미구현

필드 영역 조정 정보

---

#### `<Security>`

추출된 필드의 내용을 보안처리 해준다.

 `<Security char="*"/>`

- 속성
  - char: 보안문자

보안필드 처리 방식

- 추출된 필드의 전체 내용을 보안문자로 교체.
- `<Security>` 태그가 없거나 보안문자가 없으면 (`char=""` )  보안처리 안함.

---

#### `<MergeAllFields>`

true이면 추출된 모든 필드의 내용을 결합해서 하나의 필드로 만든다.

 `<MergeAllFields delimiter=" " align="left">true</MergeAllFields>`

- 속성
  - delimiter: 필드간 구분자 문자. 기본값은 " "(space)가 적용된다.
  - align: 정렬방식. no, left, top 을 지정하며 기본값은 left이다.

---

### `<ContentMapInfo>`

추출된 필드의 텍스트를 컨텐츠 정보로 교체해 준다.
필드 텍스트와 가장유사한 `<ContentValue>` 태그의 텍스트로 교체해 준다.
아래의 예에서는 4개의 `ContentValue` 중에서 가장 유사한 문자열로 교체 후 출력된다.

```xml
<ContentMapInfo>
    <ContentValueList>
        <ContentValue>면세법인사업자</ContentValue>
        <ContentValue>법인사업자</ContentValue>
        <ContentValue>일반과세자</ContentValue>
        <ContentValue>간이과세자</ContentValue>
    </ContentValueList>
</ContentMapInfo>
```

---

### `<Content>`

> TODO: 내용작성

`CharacterString` 엘리먼트의 내용 정보를 기록한다.

---

### `<Language>`

> TODO: 내용작성

언어정보를 설정한다. 언어정보는 `CharString` 엘리먼트에서 최종 필드의 문자셋을 필터링할 때 참조한다.

```xml
<LanguageInfo>
    <Language>Korean</Language>
    <!-- 미리정의된 문자 세트
         - Symbol: Ascii 범위내 기호
         - Digit: 숫자
         - Alphabet: 영어알파벳 대소문자
         - Alphabet-lower: 영어알파벳 소문자
         - Alphabet-upper: 영어알파벳 대문자
         - Hangul: 한글
         - Hanja: 한자
         - AllChar: 모든 문자
       -->
    <!-- ;(세미콜론)으로 group 구분. 특정구간은 -(하이픈)으로 표기. -(하이픈)을 문자세트로 지정하려면 --(두개연속)으로 표기 -->
    <CharacterSet>Symbol;Digit;Alphabet-lower;Alphabet-upper;Hangul;Hanja;0-9;a-z;A-Z;!@#$%--</CharacterSet>
</LanguageInfo>
```

---

### `<CorrectionInfo>`

교정정보 예시

```xml
<CorrectionInfo>
    <CorrectionDataInclude>
        <Include xmlpath="F00002001_businessRegistrationCorr.xml" correctionSetId="set1"/>
    </CorrectionDataInclude>
    <CorrectionDataSet>
        <CorrectionData>
            <SearchText type="text" similarity="90">주변긔기</SearchText>
            <NewText type="text">주변기기</NewText>
        </CorrectionData>
        <CorrectionData>
            <SearchText type="regex">커과용푿</SearchText>
            <NewText type="text">커피용품</NewText>
        </CorrectionData>
    </CorrectionDataSet>
</CorrectionInfo>
```

---

#### `<CorrectionDataInclude>`

별도의 교정정보 xml파일을 사용할 때 사용한다.

---

#### `<CorrectionData>`

교정정보를 지정한다. `SearchText`로 탐색한 텍스트를 `NewText`의 텍스트로 교체(replace)한다. 주로 오인식교정을 목적으로 사용한다.

``` xml
<CorrectionData>

    <!-- 탐색할 문자열
      type
      - text: 일반 문자열 단어
      - regex: 정규식 패턴
      - pattern: 패턴
      - charSet: 문자 세트
    -->
    <SearchText type="text" similarity="90">주변긔기</SearchText>

    <!-- 교체할 문자열
      type
      - text: 일반 문자열 단어
      - regex: 정규식 패턴
    -->
    <NewText type="text">주변기기</NewText>
</CorrectionData>
```

- 하위태그
  - `<SearchText>`: 교정할 텍스트를 탐색하기위한 정보. 사용방법은 [`<SearchText>`](#`<SearchText>`) 내용 참조 할 것.
  - `<NewText>`: 교체할 텍스트 정보. 교체방법을 type속성으로 지정한다.
    - _text_: 탐색된 문자열을 지정한 텍스트로 단순 교체한다.
    - _regex_: [regex replace](https://docs.microsoft.com/ko-kr/dotnet/standard/base-types/substitutions-in-regular-expressions) 방식으로 문자열을 교체해준다. _regex_ 타입은 반드시 `<SearchText>`의 `type`을 _regex_ 로 지정해야 한다.

    `<NewText>`의 _type_ 에 따라서 사용가능한 `<SearchText>`의 _type_ 은 아래 표와 같다.
    | NewText type | SearchText type               |
    | ------------ | ----------------------------- |
    | text         | text, regex, pattern, charSet |
    | regex        | regex                         |

```xml
<!-- "주변긔기"와 유사도 90 이상인 문자열을 "주변기기"로 교체 -->
<CorrectionData>
    <SearchText type="text" similarity="90">주변긔기</SearchText>
    <NewText type="text">주변기기</NewText>
</CorrectionData>

<!-- SearchText를 2개 이상 지정할 수 있다.
  아래 구문은 "대한민국"이나 "korea"를 찾아서 "한국"으로 교체해 준다.
 -->
<CorrectionData>
    <SearchText type="text" similarity="90">대한민국</SearchText>
    <SearchText type="text" similarity="70">korea</SearchText>
    <NewText type="text">한국</NewText>
</CorrectionData>

<!-- 아래 구문은 아래와 같이 교정해 준다.
 "안녕하세요" -> "안녕"
 "korea" -> "ko"
 -->
<CorrectionData>
    <SearchText type="regex">(안녕)(하세요)</SearchText>
    <SearchText type="regex">(ko)(rea)</SearchText>
    <NewText type="regex">$1</NewText>
</CorrectionData>

<!-- 공백을 제거해 준다.
  특별히 CorrectionData SearchText의 단독공백은 공백을 탐색해 준다.
  (일반적으로 교정이 아닌 탐색전용 SearchText인 경우에는 단독공백을 허용하지 않는다.)
  탐색된 텍스트를 제거하려면 NewText에 빈 값을 넣어준다.
 -->
<CorrectionData>
    <SearchText type="text" similarity="90">&#32;</SearchText>
    <NewText type="text"></NewText>
</CorrectionData>

<!-- 공백을 '-'로 교체해 준다. -->
<CorrectionData>
    <SearchText type="text" similarity="90">&#32;</SearchText>
    <NewText type="text">-</NewText>
</CorrectionData>
```

---

### `<OutBlock>`

추출할 필드블록 정보를 기록한다. 블록은 필드의 집합이다.

``` xml
<OutBlock id="11" multiBlock="false" instanceSortOrder="topToBottom">
    <Field>
        <ElementId></ElementId>
    </Field>
    ...
    <Field>
        <ElementId></ElementId>
    </Field>
</OutBlock>
```

- 속성
  - `id`: `OutBlock`의 id
  - `multiBlock`: 인식결과에 `OutBlock`의 내용이 2개 이상 출력되는지 지정. `true`이면 2개 이상 출력가능, `false`이면 1개만 출력되는 block이다. 기본값은 `false`이다. column이나 table의 내용을 인식할 때 동일한 필드타입을 여러개 출력하는 block일 때는 `true`로 지정해 준다. 이 속성정보는 인식과정에서 사용되지 않고, 범용인식 라이브러리를 사용한 어플리케이션 개발파트에 인식결과의 개수정보를 전달하는 것을 목적으로 사용한다.
  - `instanceSortOrder`: 블록의 인스턴스(필드)의 정렬순서를 지정한다. 아래의 값을 지정가능하다. 기본값은 `topToBottom` 이다.
    - `topToBottom`: 필드의 위치를 기준으로 위에서 아래로 정렬
    - `leftToRight`: 필드의 위치를 기준으로 왼쪽에서 오른쪽으로 정렬
    - `rightToLeft`: 필드의 위치를 기준으로 오른쪽에서 왼쪽으로 정렬
    - `inOrderIfFinding`: 탐색된 순서로 정렬

---

#### `<Field>`

> TODO: 내용작성

추출할 필드 정보를 기록한다.

``` xml
<!-- field 속성
  idNumber: 어플리케이션에서 결과필드에 접근하기위한 고유 id
  name: 어플리케이션에서 사용가능한 결과필드 명칭
-->
<!--
  서버파트관련 속성 설명
  - disabled: 필드 추출 유무.
    - true: 추출안함.
    - false: 추출함.(default). disabled 태그없으면 default 설정을 따름.
    엔진에서는
      true인 경우에는 필드추출 수행안함

  - security: 보안필드 유무
    - true: 보안필드
    - false: 일반필드 (default). security 태그없으면 default 설정을 따름.
    엔진에서는
      true인 경우에는 로그파일출력 시 보안처리 수행.(예정)
    인식결과의 필드정보에 security정보를 출력

  - category: 필드타입 분류정보
    엔진에서는
      인식결과의 필드정보에 category id number를 출력
    상세내용은 ocrData/fieldCategory.xml 참조.

  - isMain: 주요필드 유무
    - true: 주요필드
    - false: 비주요필드
    서버파트에서만 사용

  - mapId: 서버 후처리용 map id
    서버파트에서만 사용
-->
<Field idNumber="1" name="문서확인번호" disabled="false" security="false" category="NormalText.Digit" isMain="true" mapId="">
    <ElementId>2</ElementId>  <!-- 출력할 엘리먼트 id -->
</Field>
```

## 5. 응용방법

### 5.1.표추출방법

표를 추출하려면 column element를 사용해서 각 column을 추출할 수 있게 한 후 outField 에서 하나의 row로 묶을 수 있는 data를 지정해야 한다. 재무제표를 예로 들어 설명해 보자. 재무제표는 5개의 column으로 구성된다.

![재무제표](figure_2.jpg "재무제표 이미지")

서식은 다음과 같이 구성해야 한다.

```xml
<IzFormOcrXml version="0.5.0">
  <FormSet>
    <Form id="1">
      <FormPage id="page1" header="true" footer="true">
        <FormData id="1">

          <!-- 첫 컬럼 -->
          <Element id="items" type="Column">
            <!-- 컬럼 추출 내용 작성 -->
          </Element>

          <!-- 두번째 컬럼 -->
          <Element id="values1-1" type="Column">
            <!-- 컬럼 추출 내용 작성 -->

            <!-- 첫 컬럼을 기준컬럼으로 지정 -->
            <BaseColumnId>items</BaseColumnId>
          </Element>

          <!-- 세번째 컬럼 -->
          <Element id="values1-2" type="Column">
            <!-- 컬럼 추출 내용 작성 -->

            <!-- 첫 컬럼을 기준컬럼으로 지정 -->
            <BaseColumnId>items</BaseColumnId>
          </Element>

          <!-- 네번째 컬럼 -->
          <Element id="values2-1" type="Column">
            <!-- 컬럼 추출 내용 작성 -->

            <!-- 첫 컬럼을 기준컬럼으로 지정 -->
            <BaseColumnId>items</BaseColumnId>
          </Element>

          <!-- 다섯번째 컬럼 -->
          <Element id="values2-2" type="Column">
            <!-- 컬럼 추출 내용 작성 -->

            <!-- 첫 컬럼을 기준컬럼으로 지정 -->
            <BaseColumnId>items</BaseColumnId>
          </Element>

          <!-- outblock 에서 하나의 표안에 포함되는 필드를 모두 추가해 준다. -->
          <OutBlock id="1">
            <Field idNumber="3" name="과목">
              <ElementId>items</ElementId>
            </Field>
            <Field idNumber="4" name="금액(당기)1">
              <ElementId>values1-1</ElementId>
            </Field>
            <Field idNumber="5" name="금액(당기)2">
              <ElementId>values1-2</ElementId>
            </Field>
            <Field idNumber="6" name="금액(전기)1">
              <ElementId>values2-1</ElementId>
            </Field>
            <Field idNumber="7" name="금액(전기)2">
              <ElementId>values2-2</ElementId>
            </Field>
          </OutBlock>
        </FormData>
      </FormPage>
    </Form>
  </FormSet>

</IzFormOcrXml>
```

이렇게 서식을 구성하면 출력결과 형태는 아래와 같다.

```text
매출액, , 27407127178, , 26791348256
의료수입, 28319613201, , 26078493105,
...
차량유지비, 25720629, , 32695305,
```
