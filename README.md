# kakaolink-for-iris

아이리스 봇에서 카카오링크를 전송할 수 있는 모듈입니다.

---

## 요구사항

이 모듈을 사용하려면 **아이리스 봇**이 설치되어 있어야 합니다.

- [irispy-client](https://pypi.org/project/irispy-client) 설치 필요
- [아이리스 서버](https://github.com/iris-server) 실행 중이어야 함

```bash
pip install irispy-client httpx python-dotenv
```

---

## 파일 구조

```
helper/
└── KakaoLinkModule.py   # 카카오링크 전송 모듈
```

---

## 설치 방법

`KakaoLinkModule.py` 를 본인 봇 프로젝트의 `helper/` 폴더에 넣으면 됩니다.

---

## 환경변수 설정

봇 폴더에 `.env` 파일을 만들고 아래 내용을 작성하세요.

```env
KAKAO_APP_KEY=여기에_JavaScript_앱키_입력
KAKAO_ORIGIN=http://localhost:3000
```



---

## 카카오 개발자 콘솔 설정

### 1단계 — 앱 생성

1. [카카오 개발자 콘솔](https://developers.kakao.com) 접속 및 로그인
2. **내 애플리케이션** → **애플리케이션 추가하기**
3. 앱 이름, 회사명 입력 후 저장 (아무거나 가능)

### 2단계 — JavaScript 앱키 확인

1. 생성된 앱 클릭
2. 좌측 **앱 키** 탭
3. **JavaScript 키** 복사 → `.env`의 `KAKAO_APP_KEY`에 붙여넣기

### 3단계 — Web 플랫폼 등록

1. 좌측 **플랫폼** 탭
2. **Web 플랫폼 등록** 클릭
3. 사이트 도메인에 `http://localhost:3000` 입력 후 저장
4. `.env`의 `KAKAO_ORIGIN`도 동일하게 입력

### 4단계 — 메시지 템플릿 만들기

1. 좌측 **메시지 템플릿** 탭
2. **템플릿 만들기** 클릭
3. 원하는 형식으로 작성 후 저장
4. 템플릿 목록에서 **템플릿 ID** (숫자) 확인

---

## 사용 방법

### irispy.py 에 추가할 코드

**① 상단 import**
```python
from dotenv import load_dotenv
load_dotenv()
from helper.KakaoLinkModule import KakaoLink
```

**② KakaoLink 초기화**
```python
kl = None
try:
    _kl_app_key = os.environ.get("KAKAO_APP_KEY", "")
    _kl_origin  = os.environ.get("KAKAO_ORIGIN", "")
    if _kl_app_key and _kl_origin:
        kl = KakaoLink(
            iris_url=sys.argv[1],
            default_app_key=_kl_app_key,
            default_origin=_kl_origin,
        )
        print("✅ [KakaoLink] 초기화 완료")
    else:
        print("⚠️ [KakaoLink] .env 미설정")
except Exception as e:
    print(f"⚠️ [KakaoLink] 초기화 실패: {e}")
```

**③ 전송 예시**
```python
import asyncio

# 카카오링크 전송
asyncio.run(kl.send(
    receiver_name="채팅방 이름",   # 전송할 카카오톡 채팅방 이름
    template_id=12345,            # 카카오 템플릿 ID
    template_args={},             # 템플릿 변수 (없으면 빈 dict)
))
```

특정 채팅방 타입만 검색하고 싶을 때:
```python
asyncio.run(kl.send(
    receiver_name="채팅방 이름",
    template_id=12345,
    template_args={},
    search_room_type="MultiChat",   # OpenMultiChat / MultiChat / DirectChat / ALL
))
```

---

## 크레딧

- 카카오링크 원본 모듈: [kakaolink-py](https://github.com/ye-seola/kakaolink-py) by @ye-seola
