# 업비트 매도 신호 모니터링 v2.0 - GitHub Actions Edition

10분봉 + 60분봉 혼합 분석으로 급격한 가격 변동을 감지하는 자동화 시스템

## 🚀 주요 특징

### 1. 급락 빠른 감지 (NEW!)
- **10분봉 분석**: 단기 급락을 3시간 내에 감지
- **30분 주기 실행**: 기존 1시간 → 30분으로 단축
- **즉각 알림**: 급락 발생 후 최대 30분 내 알림

### 2. 다층 시간 분석
- **단기 (10분봉)**: 급격한 변동 감지
- **중기 (60분봉)**: 전체 흐름 파악
- **장기 (일봉)**: 추세 확인

### 3. 10개 지표 종합 분석
#### 가격 패턴 (4개)
- ✅ 단기 급락 (10분봉 기준)
- ✅ 12시간 고점 대비 하락
- ✅ 급등 후 하락 전환
- ✅ 변동성 급증

#### 거래량 (2개)
- ✅ 거래량 감소 추세
- ✅ 약세 다이버전스

#### 호가창 (1개)
- ✅ 매도벽 우세

#### 기술적 지표 (3개)
- ✅ RSI 과매수
- ✅ MACD 데드크로스
- ✅ 볼린저 상단 이탈

### 4. 3단계 매도 신호
- 🔴 **즉시매도** (7개 이상): 즉시 매도 권장
- 🟠 **매도준비** (5-6개): 일부 매도 고려
- 🟡 **매도검토** (3-4개): 주의 관찰 필요

## 📁 출력 파일

```
project/
├── market_data/
│   └── upbit_sell_signals_latest.json    # 최신 분석 데이터
├── analysis_reports/
│   └── upbit_sell_signals_YYYYMMDD_HHMM.md  # 시간별 리포트
└── upbit_sell_signals_v2.xlsx            # 누적 데이터 (최근 100개)
```

## 🛠️ 설치 및 설정

### 1. GitHub 저장소 설정

```bash
# 저장소 생성 및 클론
git clone https://github.com/YOUR_USERNAME/YOUR_REPO.git
cd YOUR_REPO

# 필요한 디렉토리 생성
mkdir -p .github/workflows market_data analysis_reports

# 파일 복사
cp upbit_sell_monitor_actions.py .
cp .github/workflows/upbit_sell_monitor.yml .github/workflows/
```

### 2. GitHub Secrets 설정

Repository Settings → Secrets and variables → Actions → New repository secret

```
TELEGRAM_BOT_TOKEN = "your_bot_token_here"
TELEGRAM_CHAT_ID = "your_chat_id_here"
```

### 3. GitHub Actions 활성화

- Repository Settings → Actions → General
- "Allow all actions and reusable workflows" 선택
- Workflow permissions: "Read and write permissions" 선택

### 4. 디렉토리 권한 확인

```bash
# .gitignore 설정 (필요시)
echo "*.pyc" >> .gitignore
echo "__pycache__/" >> .gitignore

# 초기 커밋
git add .
git commit -m "Initial commit: Upbit sell signal monitor v2.0"
git push origin main
```

## ⚙️ 설정 커스터마이징

workflow 파일 내 `config.py` 섹션에서 조정 가능:

### 급락 감지 민감도
```python
QUICK_DROP_THRESHOLD = 5.0  # 단기 급락 기준 (기본: 5%)
DROP_FROM_HIGH_12H_THRESHOLD = 8.0  # 12시간 고점 대비 (기본: 8%)
```

### 변동성 체크
```python
VOLATILITY_CHECK_CANDLES = 6  # 확인할 봉 개수 (기본: 6개)
VOLATILITY_THRESHOLD = 3.0  # 변동률 임계값 (기본: 3%)
```

### 매도 단계 기준
```python
SELL_STAGE_IMMEDIATE = 7  # 즉시매도: 7개 이상
SELL_STAGE_PREPARE = 5    # 매도준비: 5-6개
SELL_STAGE_REVIEW = 3     # 매도검토: 3-4개
```

## 📊 실행 주기 변경

`.github/workflows/upbit_sell_monitor.yml`:

```yaml
on:
  schedule:
    # 30분마다 (기본)
    - cron: '0,30 * * * *'
    
    # 15분마다 (더 빠른 감지)
    # - cron: '*/15 * * * *'
    
    # 1시간마다
    # - cron: '0 * * * *'
```

## 📱 텔레그램 메시지 예시

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

## 🔍 로컬 테스트

```bash
# 가상환경 생성 (권장)
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 패키지 설치
pip install pyupbit pandas numpy requests pytz ta openpyxl

# config.py 생성 (workflow의 config 섹션 복사)
cat > config.py << 'EOF'
BOT_TOKEN = "your_token"
CHAT_ID = "your_chat_id"
# ... (나머지 설정)
EOF

# 실행
python upbit_sell_monitor_actions.py
```

## 📈 성능 최적화

### API 호출 최적화
- 필터링: 변동이 있는 코인만 정밀 분석
- 병렬 처리 없음: Upbit API 제한 준수
- 0.1초 딜레이: API 과부하 방지

### GitHub Actions 최적화
- Cron: 30분 주기 (15분 단위도 가능)
- Cache: pip 패키지 캐싱
- Permissions: contents:write만 필요

## ⚠️ 주의사항

1. **투자 판단 책임**: 이 도구는 참고용이며, 최종 투자 판단은 본인의 책임입니다.
2. **API 제한**: Upbit API 사용 제한을 준수합니다.
3. **텔레그램 알림**: 과도한 알림 방지를 위해 요약만 전송합니다.
4. **데이터 보관**: 최근 100개 신호만 엑셀에 저장됩니다.

## 🐛 문제 해결

### Actions가 실행되지 않는 경우
1. Workflow permissions 확인 (Read and write)
2. Secrets 설정 확인 (TELEGRAM_BOT_TOKEN, CHAT_ID)
3. Cron 표현식 확인 (UTC 기준)

### 텔레그램 메시지가 안 오는 경우
1. Bot Token 확인
2. Chat ID 확인 (숫자만, 따옴표 없음)
3. Actions 로그에서 오류 확인

### Git push 실패
1. Workflow permissions → "Read and write permissions"
2. `.github/workflows/*.yml` 파일의 `permissions: contents: write` 확인

## 📝 버전 히스토리

### v2.0 (GitHub Actions Edition)
- ✅ 10분봉 급락 감지 추가
- ✅ 30분 주기 자동 실행
- ✅ JSON/Excel/Markdown 다중 저장
- ✅ Git 자동 commit & push
- ✅ 텔레그램 요약 전송 (스팸 방지)

### v1.0 (Original)
- 1시간봉 기반 분석
- 즉시 텔레그램 전송
- Excel 저장만 지원

## 📄 라이선스

MIT License

## 🤝 기여

이슈 및 PR 환영합니다!

## 📞 문의

- GitHub Issues
- Telegram: @your_telegram_handle

---

**⚡ 급락을 놓치지 마세요! 30분마다 자동 분석합니다.**
