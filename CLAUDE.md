# CLAUDE.md — lunar-birthday.html 프로젝트 명세서

## 1. 프로젝트 개요

사용자가 양력 생일을 입력하면 해당 날짜의 한국 음력 날짜와 절기를 조회하여,
시적인 한국어 문장으로 생일을 표현하는 단일 HTML 파일 웹 앱.

- **파일:** `lunar-birthday.html` (단일 파일, 빌드 도구 없음)
- **언어:** 바닐라 HTML / CSS / JavaScript
- **외부 의존성:** `korean-lunar-calendar` CDN 1개 + Google Fonts
- **API 호출:** 없음. 모든 로직은 클라이언트에서 실행

**결과 출력 형식:**
```
"{절기 설명 워딩}, {달 워딩}"
예: "하늘이 맑고 밝아지는 청명, 달이 막 차오르던 날"
```

---

## 2. 라이브러리

### CDN URL
```html
<script src="https://unpkg.com/korean-lunar-calendar@0.3.6/dist/korean-lunar-calendar.min.js"></script>
```

### API
```javascript
const cal = new KoreanLunarCalendar();      // window.KoreanLunarCalendar (IIFE 글로벌)
cal.setSolarDate(year, month, day);          // 양력 설정 (월 1-based)
const lunar = cal.getLunarCalendar();        // { year, month, day, isLeapMonth }
```

> ⚠️ 절기(Solar Term) 관련 메서드 없음 → 별도 하드코딩 필요

---

## 3. 절기 데이터 (24절기)

```javascript
const SOLAR_TERMS = [
  { name: '소한', month: 1,  baseDay: 6  },
  { name: '대한', month: 1,  baseDay: 20 },
  { name: '입춘', month: 2,  baseDay: 4  },
  { name: '우수', month: 2,  baseDay: 19 },
  { name: '경칩', month: 3,  baseDay: 6  },
  { name: '춘분', month: 3,  baseDay: 21 },
  { name: '청명', month: 4,  baseDay: 5  },
  { name: '곡우', month: 4,  baseDay: 20 },
  { name: '입하', month: 5,  baseDay: 6  },
  { name: '소만', month: 5,  baseDay: 21 },
  { name: '망종', month: 6,  baseDay: 6  },
  { name: '하지', month: 6,  baseDay: 21 },
  { name: '소서', month: 7,  baseDay: 7  },
  { name: '대서', month: 7,  baseDay: 23 },
  { name: '입추', month: 8,  baseDay: 7  },
  { name: '처서', month: 8,  baseDay: 23 },
  { name: '백로', month: 9,  baseDay: 8  },
  { name: '추분', month: 9,  baseDay: 23 },
  { name: '한로', month: 10, baseDay: 8  },
  { name: '상강', month: 10, baseDay: 23 },
  { name: '입동', month: 11, baseDay: 7  },
  { name: '소설', month: 11, baseDay: 22 },
  { name: '대설', month: 12, baseDay: 7  },
  { name: '동지', month: 12, baseDay: 22 },
];
```

절기 날짜는 매년 ±1~2일 변동하나, 이 앱의 시적 목적상 근사값 허용.

### 절기 워딩 테이블

| 절기 | 워딩 |
|------|------|
| 소한 | 겨울 추위가 깊어지는 소한 |
| 대한 | 한 해의 가장 추운 날, 대한 |
| 입춘 | 봄이 시작되는 입춘 |
| 우수 | 눈이 녹아 비가 내리는 우수 |
| 경칩 | 땅속 벌레가 깨어나는 경칩 |
| 춘분 | 낮과 밤이 같아지는 춘분 |
| 청명 | 하늘이 맑고 밝아지는 청명 |
| 곡우 | 봄비가 내려 씨앗을 적시는 곡우 |
| 입하 | 여름이 시작되는 입하 |
| 소만 | 만물이 가득 차오르는 소만 |
| 망종 | 씨앗을 뿌리는 망종 |
| 하지 | 낮이 가장 긴 하지 |
| 소서 | 여름 더위가 시작되는 소서 |
| 대서 | 한 해의 가장 더운 날, 대서 |
| 입추 | 가을이 시작되는 입추 |
| 처서 | 더위가 물러가는 처서 |
| 백로 | 이슬이 맺히기 시작하는 백로 |
| 추분 | 낮과 밤이 같아지는 추분 |
| 한로 | 찬 이슬이 내리는 한로 |
| 상강 | 서리가 내리기 시작하는 상강 |
| 입동 | 겨울이 시작되는 입동 |
| 소설 | 첫눈이 내리는 소설 |
| 대설 | 눈이 가장 많이 내리는 대설 |
| 동지 | 밤이 가장 긴 동지 |

---

## 4. 달 위상 워딩 (음력 일 기준)

| 음력 일 | 워딩 |
|---------|------|
| 1 | 달이 숨은 날 |
| 2–6 | 달이 막 피어나던 날 |
| 7–8 | 달이 반쯤 찬 날 |
| 9–14 | 달이 막 차오르던 날 |
| 15 | 보름달이 뜬 날 |
| 16–21 | 달이 기울기 시작한 날 |
| 22–23 | 달이 반쯤 기운 날 |
| 24–29 | 달이 저물던 날 |

---

## 5. 디자인

| 속성 | 값 |
|------|-----|
| 배경 | `#0a0a0f` |
| 주 텍스트 | `#f0e6d3` |
| 보조 텍스트 | `#8a7a6a` |
| 결과 문장 크기 | `1.8rem` |
| 폰트 | Noto Serif KR, serif 폴백 |
| 레이아웃 | Flexbox 중앙, max-width 480px |
