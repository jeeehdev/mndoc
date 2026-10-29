# 냥멍닥터 김집사 🐶🐱

반려동물의 눈·피부 사진을 올리면 AI가 의심 질환을 알려주고, 질환 정보와 가까운 동물병원까지 안내하는 웹 서비스입니다.

> 2023 AI 빅데이터 분석 기업 실무 캡스톤디자인 · 삼육대학교 AI 빅데이터 2조
> **팀 원본 저장소**: [kkkimsuji/pet-ai-diagnosis](https://github.com/kkkimsuji/pet-ai-diagnosis)
> 이 저장소는 포트폴리오용으로 최종 코드를 정리한 것입니다. 개발 당시 커밋 기록은 원본 저장소에서 확인할 수 있습니다.

| 항목 | 내용 |
| --- | --- |
| 기간 | 2023.08 ~ 2024.01 (웹 개발 2023.11 ~ 12) |
| 인원 | 4인 |
| 내 역할 | 웹 서비스 구축 — Flask 서버(`app.py`), 진단 결과 페이지, 동물병원 찾기 |

## 구성도

| <프로젝트 구성도> | <웹 서비스 구성도> |
| --- | --- |
|![](https://github.com/user-attachments/assets/c05dae03-81bb-41d2-b15e-10a626dd8fc5) | ![](https://github.com/user-attachments/assets/0af8d128-fc00-416b-b1f4-0c382216270d)|


## 내가 한 일

원본 저장소에서 당시 계정(`jeeehh`, `jeehb138`)으로 커밋 49개, Pull Request #2~#4를 통해 기여했습니다.

- **Flask 서버 구현**: 사진 업로드를 받아 안구·피부 모델에 전달하고, 결과 질환 번호로 DB를 조회해 결과 페이지를 렌더링하는 흐름 구현
- **모델 연동**: 학습된 YOLOv8 모델을 서버에 탑재하고, 분류 모델(안구)과 탐지 모델(피부)의 서로 다른 결과 형식을 각각 처리
- **진단 결과 페이지**: 결과 이미지와 질환 설명·증상·원인·치료법 표시
- **동물병원 찾기**: 카카오맵 API로 현재 위치와 키워드 기반 동물병원 검색 구현

## 왜 만들었나요

반려동물 보호자가 가장 관심 있는 주제는 건강관리지만, 병원에 가기 전 인터넷에서 믿을 만한 정보를 찾기는 어렵습니다. 반려견·반려묘 치료비 발생 원인 상위권에 피부 질환이 있다는 점에 주목해, 사진만으로 안구·피부 질환을 먼저 확인하고 병원 방문으로 이어지도록 돕는 서비스를 기획했습니다.

## 주요 기능

- **안구 질환 진단**: 눈 사진을 올리면 YOLOv8 분류 모델이 13종 질환(및 무증상) 중 가장 가능성이 높은 결과를 알려줍니다
- **피부 질환 진단**: 피부 사진에서 YOLOv8 탐지 모델이 병변 위치를 표시하고 6종 질환(및 무증상) 중 결과를 알려줍니다
- **질환 정보 안내**: 진단 결과에 맞는 질환 설명·증상·원인·치료법을 DB에서 찾아 함께 보여줍니다
- **BCS 비만도 확인**: 강아지·고양이 체형 그림을 보고 가장 비슷한 단계를 고르면 비만도와 관리 방법 영상을 안내합니다
- **동물병원 찾기**: 카카오맵 API로 현재 위치 또는 키워드로 동물병원을 검색합니다

## 동작 구조

```
사진 업로드 → Flask 서버(app.py) → RGB 변환
  ├ 안구: YOLOv8 분류 모델 → 1순위 질환
  └ 피부: YOLOv8 탐지 모델 → 병변 위치 표시 + 질환
→ 질환 번호로 SQLite(DB_eye / DB_sk) 정보 조회 → 결과 이미지와 함께 진단 결과 페이지 표시
```

## 기술 스택

| 구분 | 기술 |
| --- | --- |
| Backend | Python, Flask, SQLite, SQLAlchemy, pandas |
| AI | YOLOv8 (Ultralytics) |
| Frontend | HTML, CSS, JavaScript, Jinja2 템플릿 |
| API | Kakao Maps API |

## 데이터

- AI-Hub [반려동물 안구 질환 데이터](https://www.aihub.or.kr/aihubdata/data/view.do?currMenu=&topMenu=&aihubDataSe=data&dataSetSn=562) (일반카메라 데이터)
- AI-Hub [반려동물 피부 질환 데이터](https://www.aihub.or.kr/aihubdata/data/view.do?currMenu=&topMenu=&aihubDataSe=data&dataSetSn=561) (일반카메라 데이터)
- 질환별 설명·증상·원인·치료법은 `DB.db`의 `DB_eye`, `DB_sk` 테이블로 관리

## 문제 해결

**로컬 학습 시 멀티프로세싱 오류**
Colab에서 실행되던 학습 코드를 로컬(Windows)에서 실행하자 멀티프로세싱 관련 오류가 발생했습니다. 학습 환경을 리눅스와 Jupyter Notebook으로 옮겨 해결했습니다.

**데이터 불균형**
질환별 이미지 수 차이가 커 모델이 특정 질환에 치우치는 문제가 있었습니다. 클래스별 데이터 수를 맞춰 다시 구성해 해결했습니다.

## 실행 방법

```bash
pip install flask ultralytics sqlalchemy pandas pillow
python app.py
```

브라우저에서 `http://localhost:5000`으로 접속합니다. 모델 파일과 DB를 상대 경로로 읽기 때문에 `app.py`가 있는 폴더에서 실행해야 합니다.

## 아쉬운 점과 개선 방향

- 진단 결과의 "전문 병원" 정보는 실제 병원 데이터가 아닌 예시입니다. 질환별 전문 병원 데이터를 확보하지 못했기 때문입니다.
- 결과 이미지를 하나의 고정된 파일명으로 저장해, 여러 사용자가 동시에 진단하면 서로의 결과 이미지가 덮어써질 수 있습니다. 지금이라면 요청마다 고유한 파일명을 쓰고, 모델 추론을 별도 API로 분리하겠습니다.
- 질환이 탐지되지 않은 사진에 대한 예외 처리가 부족해, 이 경우 안내 화면을 보여주도록 개선이 필요합니다.


## 화면
[메인]  
![](https://github.com/user-attachments/assets/ff0cbbcd-739b-40d7-8982-0b7e7d3d1f3a)  

[진단]  
|![](https://github.com/user-attachments/assets/6e2df083-9441-4708-b323-908deb26df54)|![](https://github.com/user-attachments/assets/cfd4c5fe-c8d8-4713-a79b-8292cad9853c)|![](https://github.com/user-attachments/assets/30a9b665-c47a-4209-a7de-c725e0224e2b)|
|--|--|--|



[병원 찾기]  
![](https://github.com/user-attachments/assets/7de2b778-f7ef-4400-bb24-bafc986d5bcc)


[BCS비만도 측정]  

|![](https://github.com/user-attachments/assets/70d1d8c0-dfcc-4cd2-a64f-c9c8b616e538)|![](https://github.com/user-attachments/assets/b0682d05-0b49-4b07-b6dd-f1581e831c4b)|![](https://github.com/user-attachments/assets/6c16dae0-fc32-4298-8153-9c9a50b85c75)|
|--|--|--|

---
## 결과 보고서

- [AI빅데이터 2팀 결과보고서 (PDF)](https://github.com/kkkimsuji/team_pet/files/13788241/AI._2._.pdf)
