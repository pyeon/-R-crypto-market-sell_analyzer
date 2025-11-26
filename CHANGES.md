# 업비트 매도 신호 모니터 - GitHub Actions 변환 완료

## 📋 변경 사항 요약

### 1. 핵심 변경
✅ **개별 알림 → 요약 알림**
- 기존: 매도 신호 발견 시마다 즉시 텔레그램 전송
- 개선: 전체 분석 완료 후 요약만 1회 전송 (스팸 방지)

✅ **다중 포맷 저장**
- JSON: `market_data/upbit_sell_signals_latest.json`
- Markdown: `analysis_reports/upbit_sell_signals_YYYYMMDD_HHMM.md`
- Excel: `upbit_sell_signals_v2.xlsx` (기존과 동일)

✅ **Git 자동화**
- 분석 후 자동으로 Git commit & push
- GitHub에서 모든 이력 확인 가능

✅ **30분 주기 자동 실행**
- GitHub Actions cron: `0,30 * * * *`
- 급락을 더 빠르게 감지 (기존 1시간 → 30분)

## 📁 생성된 파일

```
outputs/
├── upbit_sell_monitor_actions.py    # GitHub Actions 최적화 버전
├── config.example.py                 # 설정 예제
├── README.md                         # 전체 가이드
└── .github/
    └── workflows/
        └── upbit_sell_monitor.yml    # GitHub Actions workflow
```

## 🚀 사용 방법

### 1단계: GitHub 저장소 준비
```bash
# 새 저장소 생성 또는 기존 저장소 사용
cd your-repository

# 필요한 디렉토리 생성
mkdir -p .github/workflows market_data analysis_reports
```

### 2단계: 파일 배치
```bash
# 생성된 파일들을 저장소에 복사
cp upbit_sell_monitor_actions.py .
cp config.example.py .
cp README.md .
cp -r .github/workflows .github/
```

### 3단계: GitHub Secrets 설정
Repository → Settings → Secrets and variables → Actions

새로운 Secrets 추가:
- `TELEGRAM_BOT_TOKEN`: 텔레그램 봇 토큰
- `TELEGRAM_CHAT_ID`: 텔레그램 채팅 ID

### 4단계: GitHub Actions 권한 설정
Repository → Settings → Actions → General

1. **Workflow permissions**: "Read and write permissions" 선택
2. **Actions permissions**: "Allow all actions and reusable workflows" 선택

### 5단계: 초기 커밋 및 푸시
```bash
git add .
git commit -m "Add: 업비트 매도 신호 모니터링 v2.0 (GitHub Actions)"
git push origin main
```

### 6단계: Actions 실행 확인
- Repository → Actions 탭
- "Upbit Sell Signal Monitor" workflow 확인
- 30분마다 자동 실행 시작

## 📊 주요 기능 비교

| 항목 | 기존 (v1) | 개선 (v2) |
|------|----------|----------|
| 실행 방식 | 수동 실행 | GitHub Actions 자동 |
| 실행 주기 | 수동 | 30분마다 자동 |
| 텔레그램 알림 | 신호마다 전송 | 요약만 1회 전송 |
| 데이터 저장 | Excel만 | JSON+Excel+Markdown |
| 이력 관리 | 로컬 파일만 | Git 자동 커밋 |
| 급락 감지 | 1시간 지연 가능 | 최대 30분 지연 |

## ⚙️ 설정 커스터마이징

### 실행 주기 변경
`.github/workflows/upbit_sell_monitor.yml`에서 cron 수정:

```yaml
schedule:
  # 현재: 30분마다
  - cron: '0,30 * * * *'
  
  # 15분마다로 변경하려면:
  # - cron: '*/15 * * * *'
  
  # 1시간마다로 변경하려면:
  # - cron: '0 * * * *'
```

### 매도 신호 민감도 조정
`.github/workflows/upbit_sell_monitor.yml`의 config 섹션:

```python
# 더 민감하게 (더 많은 신호)
QUICK_DROP_THRESHOLD = 3.0  # 5.0 → 3.0
SELL_STAGE_REVIEW = 2       # 3 → 2

# 덜 민감하게 (엄격한 신호)
QUICK_DROP_THRESHOLD = 7.0  # 5.0 → 7.0
SELL_STAGE_REVIEW = 4       # 3 → 4
```

## 📱 텔레그램 메시지 예시

### 매도 신호 있을 때
```
🔍 업비트 매도 신호 분석 완료
━━━━━━━━━━━━━━━━━━━━━
⏰ 분석시각: 2025-01-15 14:30:00
📊 분석대상: 250개 코인
🎯 매도신호: 3개 발견

【 단계별 분류 】
🔴 즉시매도: 1개
🟠 매도준비: 1개
🟡 매도검토: 1개

【 주요 매도 신호 】

🔴 BTC
💰 95,500,000원 (8/10)
📌 단기급락 30분전 -6.2%, 12시간 고점대비 -9.1%, RSI 과매수

🟠 매도준비: ETH, XRP

━━━━━━━━━━━━━━━━━━━━━
📁 상세 분석 결과:
- JSON: market_data/
- Excel: upbit_sell_signals_v2.xlsx
- Report: analysis_reports/
```

### 매도 신호 없을 때
```
🔍 업비트 매도 신호 분석 완료
━━━━━━━━━━━━━━━━━━━━━
⏰ 분석시각: 2025-01-15 14:30:00
📊 분석대상: 250개 코인
🎯 매도신호: 0개 발견

✅ 현재 조건에 맞는 매도 신호 없음

━━━━━━━━━━━━━━━━━━━━━
📁 상세 분석 결과:
- JSON: market_data/
- Excel: upbit_sell_signals_v2.xlsx
- Report: analysis_reports/
```

## 🔍 로컬 테스트 (선택사항)

Actions 배포 전에 로컬에서 테스트:

```bash
# 가상환경 생성
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 패키지 설치
pip install pyupbit pandas numpy requests pytz ta openpyxl

# config.py 생성
cp config.example.py config.py
# config.py를 편집기로 열어서 실제 값 입력

# 실행
python upbit_sell_monitor_actions.py

# 결과 확인
ls market_data/
ls analysis_reports/
```

## ⚠️ 주의사항

1. **Workflow Permissions 필수**
   - "Read and write permissions" 없으면 Git push 실패
   
2. **Secrets 정확히 입력**
   - TELEGRAM_BOT_TOKEN: 따옴표 없이 토큰만
   - TELEGRAM_CHAT_ID: 숫자만 (따옴표 없음)

3. **Cron은 UTC 기준**
   - `0,30 * * * *` = 매일 매 시간 0분, 30분 (UTC)
   - 한국 시간 기준 +9시간 차이

4. **API 사용량**
   - 30분마다 실행 = 하루 48회
   - Upbit API 무료이므로 제한 없음

5. **Git 이력 관리**
   - 매번 커밋 생성 (하루 48개 커밋)
   - 필요시 squash 가능

## 🐛 트러블슈팅

### Q1: Actions가 실행되지 않아요
**A**: Workflow permissions 확인
- Settings → Actions → General → Workflow permissions
- "Read and write permissions" 선택

### Q2: 텔레그램 메시지가 안 와요
**A**: Secrets 확인
- BOT_TOKEN과 CHAT_ID가 정확한지 확인
- Actions 로그에서 오류 메시지 확인

### Q3: Git push 실패
**A**: yml 파일 확인
```yaml
permissions:
  contents: write  # 이 줄이 있는지 확인!
```

### Q4: 너무 많은 알림이 와요
**A**: 임계값 조정
- `SELL_STAGE_REVIEW` 값을 높이기 (3 → 4)
- `QUICK_DROP_THRESHOLD` 값을 높이기 (5.0 → 7.0)

### Q5: 급락을 놓쳤어요
**A**: 실행 주기 단축
- cron을 15분 단위로 변경: `*/15 * * * *`
- 단, Actions 실행 횟수 증가 (하루 96회)

## 📈 성능 정보

- **분석 시간**: 약 3-5분 (250개 코인 기준)
- **API 호출**: 코인당 최대 4회 (필터링으로 실제는 적음)
- **저장 용량**: 
  - JSON: ~100KB
  - Markdown: ~50KB
  - Excel: ~50KB (최근 100개)

## 🎯 다음 단계

1. **Actions 실행 확인**
   - 첫 실행 후 market_data/, analysis_reports/ 폴더 생성 확인
   
2. **텔레그램 알림 확인**
   - 30분 후 요약 메시지 수신 확인
   
3. **데이터 축적**
   - 며칠간 데이터 쌓이면 패턴 분석 가능
   
4. **임계값 최적화**
   - 실제 데이터 보고 민감도 조정

## 📞 추가 문의

문제가 있으면:
1. Actions 로그 확인 (Repository → Actions → 해당 run)
2. README.md 참고
3. GitHub Issues 등록

---

**✅ 변환 완료! 이제 GitHub에 푸시하고 30분마다 자동으로 매도 신호를 감지합니다.**
