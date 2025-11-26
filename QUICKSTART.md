# 🚀 빠른 시작 가이드 (5분 완성)

## 1️⃣ GitHub 저장소 생성 (1분)

```bash
# 새 저장소 생성 (GitHub 웹사이트에서)
# 또는 기존 저장소 사용

# 저장소 클론
git clone https://github.com/YOUR_USERNAME/YOUR_REPO.git
cd YOUR_REPO
```

## 2️⃣ 파일 배치 (1분)

```bash
# 다운로드한 파일들을 저장소에 복사
mkdir -p .github/workflows market_data analysis_reports

# 파일 복사
cp upbit_sell_monitor_actions.py .
cp config.example.py .
cp README.md .
mkdir -p .github/workflows
cp .github/workflows/upbit_sell_monitor.yml .github/workflows/

# Git 추가
git add .
git commit -m "Add: 업비트 매도 신호 모니터링 시스템"
git push origin main
```

## 3️⃣ GitHub Secrets 설정 (2분)

Repository → Settings → Secrets and variables → Actions → New repository secret

추가할 Secrets:

```
Name: TELEGRAM_BOT_TOKEN
Value: 1234567890:ABCdefGHIjklMNOpqrsTUVwxyz
(따옴표 없이 토큰만 입력)

Name: TELEGRAM_CHAT_ID  
Value: 123456789
(따옴표 없이 숫자만 입력)
```

### 텔레그램 설정 방법

#### 봇 토큰 받기:
1. 텔레그램에서 `@BotFather` 검색
2. `/newbot` 명령어 입력
3. 봇 이름 입력 (예: My Upbit Monitor)
4. 봇 사용자명 입력 (예: my_upbit_monitor_bot)
5. 받은 토큰 복사 → TELEGRAM_BOT_TOKEN

#### 채팅 ID 받기:
1. 텔레그램에서 `@userinfobot` 검색
2. 시작 버튼 클릭
3. Id 숫자 복사 → TELEGRAM_CHAT_ID
4. 또는 봇에게 메시지 보낸 후:
   ```bash
   curl https://api.telegram.org/bot<YOUR_TOKEN>/getUpdates
   # "chat":{"id":123456789} 부분 확인
   ```

## 4️⃣ GitHub Actions 권한 설정 (1분)

Repository → Settings → Actions → General

**필수 설정:**

1. **Workflow permissions**
   - ✅ "Read and write permissions" 선택
   - ⬜ "Allow GitHub Actions to create and approve pull requests" (선택사항)

2. **Actions permissions**
   - ✅ "Allow all actions and reusable workflows"

저장 버튼 클릭!

## 5️⃣ 완료! ✅

이제 다음이 자동으로 작동합니다:

- ⏰ **30분마다 자동 실행**
- 📊 **250개 코인 분석**
- 🔔 **텔레그램 요약 알림**
- 💾 **JSON/Excel/Markdown 저장**
- 🔄 **Git 자동 커밋**

## 📱 첫 실행 확인

### 수동으로 첫 실행 (추천)

1. Repository → Actions 탭
2. "Upbit Sell Signal Monitor" 클릭
3. "Run workflow" 버튼 클릭
4. "Run workflow" 확인

### 결과 확인

**텔레그램 메시지 예시:**
```
🔍 업비트 매도 신호 분석 완료
━━━━━━━━━━━━━━━━━━━━━
⏰ 분석시각: 2025-01-15 14:30:00
📊 분석대상: 250개 코인
🎯 매도신호: 0개 발견

✅ 현재 조건에 맞는 매도 신호 없음
```

**GitHub 저장소:**
```
your-repo/
├── market_data/
│   └── upbit_sell_signals_latest.json  ✅ 생성됨
├── analysis_reports/
│   └── upbit_sell_signals_20250115_1430.md  ✅ 생성됨
└── upbit_sell_signals_v2.xlsx  ✅ 업데이트됨
```

## ⚙️ 설정 변경 (선택사항)

### 더 빠른 감지 (15분마다)

`.github/workflows/upbit_sell_monitor.yml` 수정:

```yaml
schedule:
  - cron: '*/15 * * * *'  # 30분 → 15분
```

### 더 민감한 신호

workflow 파일의 config 섹션:

```python
QUICK_DROP_THRESHOLD = 3.0  # 5.0 → 3.0 (더 민감)
SELL_STAGE_REVIEW = 2  # 3 → 2 (더 많은 신호)
```

변경 후 Git push:
```bash
git add .github/workflows/upbit_sell_monitor.yml
git commit -m "Update: 감지 주기 15분으로 단축"
git push
```

## 🔧 문제 해결

### ❌ Actions가 실행되지 않아요

**체크리스트:**
- [ ] Workflow permissions = "Read and write permissions"?
- [ ] Secrets 2개 모두 입력? (BOT_TOKEN, CHAT_ID)
- [ ] Actions permissions = "Allow all actions"?
- [ ] .github/workflows/upbit_sell_monitor.yml 파일 있음?

### ❌ 텔레그램 메시지 안 와요

**확인사항:**
1. Actions 로그 확인:
   - Repository → Actions → 실패한 run 클릭
   - "Run sell signal monitor" 단계 확인
   - 오류 메시지 확인

2. Secrets 다시 확인:
   - 토큰에 따옴표 없는지
   - 채팅 ID가 숫자인지
   - 공백이 없는지

3. 봇 테스트:
   ```bash
   curl -X POST "https://api.telegram.org/bot<YOUR_TOKEN>/sendMessage" \
        -d "chat_id=<YOUR_CHAT_ID>" \
        -d "text=Test message"
   ```

### ❌ Git push 실패

**해결방법:**

1. workflow 파일 확인:
   ```yaml
   permissions:
     contents: write  # 이 줄 있어야 함!
   ```

2. 권한 재확인:
   - Settings → Actions → General
   - Workflow permissions = "Read and write permissions"

## 📞 추가 도움

- 📖 **전체 문서**: `README.md`
- 📝 **변경사항**: `CHANGES.md`
- 🐛 **문제 발생시**: GitHub Issues

---

**🎉 축하합니다! 30분마다 자동으로 매도 신호를 감지합니다.**

**⏰ 다음 실행 시간**: Actions 탭에서 확인 가능  
**📊 누적 데이터**: market_data/ 폴더에서 확인  
**📈 상세 리포트**: analysis_reports/ 폴더에서 확인
