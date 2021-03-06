# 프로젝트 구조

현재 2020년 기준, 웹프론트엔드 프로젝트 폴더 구조를 정리 해 둡니다.

## Folder by Feature

모든 하위 구조는 Feature 단위로 구분 됩니다.

Feature 는 일반적으로 `기능` 위주이고, 그 뜻은 각 페이지 라우트를 기준으로 가진다는 의미 입니다.

그리고 이러한 기능들은 일반적으로 `CRUD`가 포함되며, 때에 따라 CRUD 중 하나, 혹은 그 이상을 포함 할 수 있습니다.

- [get, put, post] /example/user, [get] /example/user/{userId} - 모두 같은 Feature
- [get] /pome/user, [get] /winter/admin - 다른 Feature

모든 feature 폴더는 다음과 같이 modules 하위에 위치 합니다.

- src
  - shared (가칭) - 프로젝트를 벗어나 사내 모든 프로젝트에 공통으로 사용되는 요소들을 정의. 아직 상호간 합의된 바가 없음.
  - modules
    - common - 공용 구성요소. 공통으로 사용되는 컴포넌트, 서비스 및 모델 등을 정의한다. 현재 프로젝트 내에서만 유효하다. 기본적인 구조는 아래 설명된 `Feature 내 상세 구조` 와 같다.
    - root - 루트(/) 경로에서 사용되는 각종 요소들을 정의.
    - {feature} - 해당 `기능 범위 (Feature Scope)` 에서만 사용되는 각종 요소들을 정의.

## Feature 내 상세 구조

기능별로 가질 수 있는 폴더 구조는 아래와 같습니다.

다양한 요소들을 서술 해 놓았으나 업무가 단순하여 특정 요소가 필요 없다면 (예: 전용 message, util 등) 무리하게 만들지 않습니다.

- components - UI 컴포넌트에 대하여 정의된 곳. 아래와 같은 하위 컴포넌트 모듈로 구성 된다. 자세한 가이드는 [다음링크](atomic-design.md)를 확인 한다.
  - atoms - 더이상 쪼갤 수 없는 단일 컴포넌트.
  - combines - 최소 1개 이상의 atoms 컴포넌트를 포함되는 조합형 컴포넌트.
  - complexes - 최소 1개 이상의 combines 컴포넌트를 포함하는 복합 컴포넌트.
  - containers
    - atoms ~ complexes 에서 정의된 다양한 컴포넌트를 이용하여 최소 업무 단위를 구성하는 컴포넌트.
    - context를 이용하여 action 수행 및 state 접근등이 허용되는 유일한 곳이다.
    - 하위 컴포넌트에서 container 를 이용 시 그 컴포넌트는 현재 레벨에 관계없이 complex 로 간주된다.
  - pageContainers
    - 페이지 컴포넌트 에서만 쓰이는 특수한 컴포넌트.
    - header/footer 와 같은 반복 요소를 포함하고, 페이지 전체 layout 을 잡아주는 역할을 한다.
    - 또 한 title 이나 open-graph 와 같은 meta tag 요소들도 이들이 관리 한다.
- contexts
  - 페이지, 혹은 컨테이너 컴포넌트의 상태 관리를 하는 컨텍스트를 정의한 곳. 컨텍스트는 특별한 사유가 아닌한 `contextInjector`를 통하여 정의하도록 한다.
  - 사용되는 state 는 반드시 models/{feature}.ui.ts 의 내용을 기반으로 구성되어야 한다.
- hoc
  - High-Order Component 를 정의해 둔 곳. HOC는 가능한한 `Decorator Pattern`을 준수하여 작성 한다.
  - hoc 를 거친 컴포넌트는 atomic design 기준, 사용된 atomic level 을 유지한다.
- hooks
  - custom hook 을 정의한 곳. 이러한 hook 은 반드시 UI Component 내에서만 사용토록 한다.
- interactors
  - context 의 상태를 이용하여 비동기 및 자료조작 등 전반적인 비즈니스 로직이 구성되는 곳이다.
  - 간단한건 context 선언 시 함께 작성해도 되나, 가급적 이 곳에 관련 인터렉터를 구성하길 권장한다.
  - 각종 data service 는 이곳에서만 이용한다.
- messages
  - 긴 메시지가 쓰인다면 이곳에 정의하고 import 해서 사용한다.
  - template 성향의 메시지도 이 곳에 정의해서 i18n 등과 함께 조합하여 사용한다.
  - 구성 되는 메시지 상수는 접두어로 `MSG_`를 쓰도록 한다.
- models - 업무에 쓰이는 각종 데이터 모델을 정의한다. 크게 server model, ui model 등이 있다.
  - {feature[subFeature]}.server.ts - 서버에서 온 도메인 모델 모음.
  - {feature[subFeature]}.ui.ts - [Presentation Model Pattern](https://martinfowler.com/eaaDev/PresentationModel.html) 에 따라 UI 로직에 적합하게 변환된 모델. Context 의 State 는 반드시 이 모델을 기반으로 구성되어야 한다.
    - 만약 서버 모델이 UI 친화적일 경우 (가능한한) 단순히 type 문으로 alias 하여 넘겨주고 이 것을 이용토록 한다.
- pages
  - 특정 라우트에 대응되는 페이지 자체를 렌더링 하는 컴포넌트. 자세한 가이드는 [다음링크](??)를 확인 한다.
  - 각종 쿼리/패스 파라미터 처리와 하위 컨테이너에게 넘기고 페이지 제목 출력(웹브라우저 탭 제목)을 담당한다.
  - PageContainer 를 사용하여 페이지의 공용 렌더링 처리부분을 맡는다.
  - context 를 이용하여 하위 컴포넌트에게 상태를 전달 할 수 있다.
- selectors
  - 특정 데이터의 필요한 부분만 가져오는 역할.
  - redux store, context 의 state 내 필요한 값만 가져오거나 query parameter 의 기본값 처리를 위한 방어 코드가 삽입될 수 있다.
- services - 각종 데이터 서비스가 정의되는 곳. 크게 api 를 통한 데이터 교환, 가져온 데이터를 특정 데이터로의 변환, 유효성 검사 등이 포함 된다.
  - {feature[subFeature]}.data.ts
    - interactor 및 effect 를 위한 데이터 통신을 하는 역할을 맡는다.
    - 작성 시 유의점은, 이 서비스를 이용하는 외부에서는 어떠한 출처를 이용하여 주는지 알 필요 없도록 해야 한다는 것이다. (관심사 분리)
    - 제공되는 자료의 출처는 정해진 사내 Backend API, CDN Static, assets, third party API, Local Cache, Local File, Storage 등이 있다.
    - 특별한 이유가 없다면 Promise 로 return 한다.
  - {feature[subFeature]}.create.ts - 지정된 모델에 맞추어 기초 데이터를 만드는 함수가 정의된 곳. 혹은 테스트용 mock data 생성기도 정의될 수 있다.
  - {feature[subFeature]}.convert.ts - 모델A to 모델B 혹은 그 반대, 또는 모델A 에서 모델C 로의 추출 등의 데이터 변환을 맡는 각종 함수가 정의된다.
  - {feature[subFeature]}.validate.ts - 입력 데이터에 대한 유효성 검증을 맡는 기능이 정의된다.
  - {feature[subFeature]}.update.ts - List 나 Detail 형태의 데이터를 수정함에 있어 로직이 다소 복잡하다면, 이 곳에 update 함수를 정의하여 사용한다.
  - {feature[subFeature]}.parser.ts - json 이 아닌 특정 데이터 (binary, html, xml, plain text 등)를 업무에서 사용가능한 모델로 분석하고 변환하는 기능이 정의된다.
- styles
  - 이 곳에서만 쓰이는 특정 스타일을 지정하는 곳. 접두어로 `css`를 반드시 쓰도록 한다. 이 후 공용으로 쓰이게 되면 common 모듈로 옮긴다.
- util
  - 이 곳에서만 쓰이는 특정 유틸리티를 지정하는 곳. 이 후 공용으로 쓰이게 되면 common 모듈로 옮긴다.
- routes.ts
  - 현재 feature 에 소속된 각종 page 컴포넌트의 라우팅 설정을 object array 로 선언해 놓은 곳.
  - 리팩토링이 끝나면 선언해서 쓰도록 한다.

## 작성 시 유의사항

특별한 사유가 없다면, 일반적으로 비즈니스 로직은 조그만 순수함수(Pure Function)를 여러개 만들고 조합하여 구현 합니다.

순수함수를 이용하는 까닭은, 하나의 문제에 대하여 1가지 해결 방법만을 제시하기에 테스트하기 쉽고, 유지보수 및 타 개발자가 이해하기도 쉽기 때문입니다.

그렇다고 억지로 함수형으로만 해보려는 고민을 억지로 하지 않아도 됩니다.

필요하다면 고차함수(HOF - High Order Function)를 써도 무방하며 여러가지 루틴 발생된다면 그에 맞는 각자의 함수로 쪼개어 만들고 조합 시키도록 합시다.

## 라우팅 정의

현재 next.js 를 이용치 않는 관계로, react-router-dom 의 Router 요소를 이용하게 됩니다.

이러한 라우팅 요소는 캡슐화 하고, 라우팅 자료만 받아서 처리하는 별도의 컨테이너를 두는 것이 좋지만,

새로 정의된 아키텍처를 구성하여 리팩토링 되는 과정중에는 예외적으로 기존 라우팅 시스템을 이용하여 구성합니다.
