# 편다 법적 고지 페이지

Google Play / App Store 심사에 필요한 공개 URL 3종.

| 파일 | 용도 |
| --- | --- |
| `privacy.html` | 개인정보처리방침 — 스토어 등록 정보 + 데이터 보안 양식 |
| `terms.html` | 이용약관 |
| `account-deletion.html` | 계정 삭제 요청 URL — Play "앱 활동 데이터 삭제" 요구사항 |
| `index.html` | 위 세 페이지로 가는 목차 |

## ⚠️ 출시 전 반드시 처리할 것

`terms.html`의 **전화번호 행이 주석 처리돼 있습니다.** 번호가 아직 없어 임시로 숨긴 것이며,
전자상거래법 제13조상 통신판매업자의 표시 의무 항목이라 **출시 전에 반드시 되살려야 합니다.**

```bash
grep -n "TODO(출시 전 필수)" legal/terms.html
```

070 인터넷전화 등으로 대표번호를 확보한 뒤 해당 주석을 풀고 번호를 채워 다시 게시하세요.

## 1. 채워야 할 항목

전체 파일에서 `{{ }}`로 감싼 부분을 실제 값으로 바꿔야 합니다.

```bash
grep -rn "{{" legal/
```

| 항목 | 설명 |
| --- | --- |
| `{{운영자명}}` | 개인이면 실명 또는 상호, 사업자면 법인명 |
| `{{시행일}}` / `{{최종 수정일}}` | 실제 게시일 |
| `{{문의 이메일}}` | 이용자 문의를 실제로 받는 주소 |
| `{{성명}}`, `{{소속/직위}}` | 개인정보 보호책임자 (1인 운영이면 본인) |
| `{{Supabase 프로젝트 리전 국가}}` | Supabase 대시보드 → Settings → General → Region |
| `{{스토어 등록명}}` | Play Console / App Store Connect에 등록할 앱 이름 (예: `편다 - 편입은단어다`) |
| `{{대표자 성명}}` · `{{사업자등록번호}}` · `{{사업장 주소}}` · `{{전화번호}}` | 사업자등록증 기재 내용 (`terms.html` 사업자 정보) |
| `{{통신판매업 신고번호}}` | 관할 시·군·구청 신고 후 부여되는 번호 |

> 운영자명은 `주식회사 트레일서비스`로 이미 반영돼 있습니다(법인 등기 명칭).

### 서비스 명칭 표기 규칙

- 세 문서 모두 **첫 등장에서 `편다(편입은단어다)`로 병기**하고, 이후에는 "서비스"로 축약합니다.
  출원 중인 두 표장을 공식 문서에서 함께 쓰는 형태입니다.
- **`account-deletion.html`의 앱 이름은 Play 등록 정보의 앱 이름과 글자까지 동일해야 합니다.**
  심사자가 이 페이지가 해당 앱의 것인지 대조하므로, 다르면 반려 사유가 될 수 있습니다.
- 「편다」는 상표 심사 중이므로 **® 표기를 쓰지 마세요** (등록 전 사용은 허위표시 소지).

## 2. GitHub Pages로 게시하기

앱 저장소는 비공개로 두고, 법적 고지용 **공개 저장소**를 따로 만드는 방식입니다.
(GitHub Pages는 무료 플랜에서 비공개 저장소를 지원하지 않습니다.)

```bash
# 1) 이 폴더만 담은 새 저장소 준비
cd legal
git init
git add .
git commit -m "Add privacy policy, terms, and account deletion pages"

# 2) GitHub에 공개 저장소 생성 후 연결 (gh CLI 사용 시)
gh repo create pyeonda-legal --public --source=. --push
```

그 다음 GitHub 저장소 → **Settings → Pages** → Source를 `Deploy from a branch`,
Branch를 `main` / `/ (root)`로 지정하고 저장합니다. 1~2분 뒤 아래 주소로 열립니다.

## 2-1. 커스텀 도메인 연결 (필수)

앱은 GitHub 주소가 아니라 **`https://pyeonda.trailservice.net`** 을 가리킵니다
(`src/constants/legal.ts`의 `LEGAL_BASE_URL`). 이 도메인을 연결해야 링크가 열립니다.

자체 도메인을 쓰는 이유 — 이 주소는 앱에 박히고 스토어 등록 정보에도 들어가서, 나중에
바꾸려면 재빌드와 스토어 수정이 필요하고 이미 배포된 앱은 계속 옛 주소를 가리킵니다.
자체 도메인이면 호스팅을 옮겨도 주소가 그대로입니다.

1. **DNS에 CNAME 레코드 추가** (도메인 관리 콘솔)
   ```
   이름(호스트)  pyeonda
   유형          CNAME
   값            <github-사용자명>.github.io
   ```
2. 저장소 → **Settings → Pages → Custom domain** 에 `pyeonda.trailservice.net` 입력 후 저장
   (이 폴더의 `CNAME` 파일이 함께 커밋되면 자동으로 채워집니다)
3. **Enforce HTTPS** 체크 — 인증서 발급에 몇 분 걸릴 수 있습니다

연결이 끝나면 아래 주소로 열립니다.

```
https://pyeonda.trailservice.net/privacy.html
https://pyeonda.trailservice.net/terms.html
https://pyeonda.trailservice.net/account-deletion.html
```

## 3. 각 URL을 등록할 위치

**Google Play Console**
- 앱 콘텐츠 → 개인정보처리방침 → `privacy.html` URL
- 앱 콘텐츠 → 데이터 보안 → 계정 삭제 URL → `account-deletion.html` URL
- 스토어 설정 → 앱 카테고리 → 개발자 연락처 이메일

**App Store Connect**
- 앱 정보 → 개인정보 보호 정책 URL → `privacy.html` URL
- 앱 정보 → 라이선스 계약 (기본 EULA 대신 쓰려면) → `terms.html`

## 4. 앱 안에서도 링크하기

Play는 스토어 등록 정보의 개인정보처리방침 URL을 필수로 요구하고,
앱 내에서도 접근 가능하기를 권장합니다. 설정 화면에 세 링크를 추가하는 것을 권장합니다.

## 주의

이 문서들은 앱의 실제 동작과 데이터베이스 스키마를 근거로 작성한 초안입니다.
법률 자문이 아니므로, 특히 사업자 정보 · 환불 조항 · 보관 기간은 실제 운영 방식과 맞는지
확인하시고 필요하면 전문가 검토를 받으시기 바랍니다.
