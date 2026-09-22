# 1주차 과제 — 키가 왜 중요한가

이번 주 수업(키 유출 체험)을 스스로 정리하는 과제입니다.
**정답을 맞히는 게 아니라, 직접 해보고 자기 말로 적는 것**이 목적입니다.

> ⚠️ 공통 규칙
> - **가짜 키만** 사용하세요. 진짜 토큰/비밀번호는 절대 쓰지 마세요.
> - 스크린샷에 **토큰·이메일 등 실제 값이 찍히지 않게** 가리세요.
> - 제출은 **본인 레포에 PR**로 합니다. (예: `docs/week01.md` 파일을 추가하는 PR)
> - 마감: **다음 모임 전날 자정**

---

## 기본 과제 (전원 필수)

본인 레포에 `docs/week01.md` 를 만들어 아래를 정리하고 PR을 올리세요.

### 1) 토큰 실습 정리
- 내 토큰으로 `https://api.github.com/user` 를 호출했을 때 **무엇이 보였는지** (실제 값은 가리고, 항목만: 예 "이메일, 이름, private 레포 목록")

이메일, 이름, 주소 등의 개인정보 및 프로필·팔로워·저장소 API 주소. 해당 URL만 호출했을 땐, 프라이빗 레포 목록(숫자만)

- 토큰을 **Revoke** 한 뒤 같은 호출이 어떻게 달라졌는지

Bad credentials, 401상태 응답

- **Fine-grained token**(레포 1개 + 만료일)으로 새로 발급한 뒤, 보이는 범위가 어떻게 줄었는지

내가 프로필에 띄운 개인정보 및 주소들은 그대로 보이지만, 프라이빗 레포 관련 항목이나 2FA설정 등은 안보임

- 한 줄 소감: "권한을 좁게 준다"가 왜 중요한지

의도치 않게 토큰이 유출되었을 때, 권한이 많은 토큰이면 프라이빗한 정보나 데이터의 보안이 위험함 -> 최소 권한 규칙 지키기


### 2) 키가 새는 실수 5가지 정리
수업에서 본 5가지를 각각 **"무엇을 / 왜 위험한지 / 어떻게 하면 안 새는지"** 한두 줄로:
1. 코드에 하드코딩 / 키 수집 봇이나 사람 등에게 키가 유출될 수 있음 /
    키는 .env 또는 운영체제 환경변수에 저장하고, .env는 .gitignore에 등록함. GitHub Actions에서는 Repository secrets를 사용하며, 만료기간과 최소 권한을 설정하고 커밋 전 비밀정보 검사도 실시함.

2. `.env` 만 만들고 `.gitignore` 안 함 / .env도 일반 파일이므로 실수로 커밋하면 키가 저장소에 그대로 올라감 /
    .gitignore에 .env를 추가하고, 커밋 전에 git status로 .env가 포함되지 않았는지 확인함.

3. `.gitignore` 를 나중에 추가 (이미 추적 중인 파일) / 이미 Git이 추적 중인 파일은 나중에 .gitignore에 등록해도 계속 커밋될 수 있음
    git rm --cached .env로 Git의 추적을 해제하고, 이미 키를 푸시했다면 기록 삭제만 믿지 말고 해당 키를 즉시 폐기한 뒤 새로 발급함.

4. 변형 파일 (`.env.local` 등) / .env만 제외하면 .env.local, .env.production 등 다른 환경설정 파일이 저장소에 올라갈 수 있음 /
    .gitignore에 .env*를 등록하되, 공유용 예시 파일인 .env.example만 예외 처리하고 실제 키는 넣지 않음.

5. PR·댓글에 붙여넣기 (수정해도 편집 기록에 남음) / 내용을 수정하거나 삭제해도 알림, 이메일, 편집 기록, 로그 등에 키가 남을 수 있음 /
    키 대신 변수명이나 일부를 가린 예시를 사용하고, 실수로 붙여넣었다면 게시물 수정에 그치지 말고 즉시 키를 폐기하고 재발급함.
   
---

## 추가 과제 (하고 싶은 사람만)

여기까지 하면 2주차(도구로 자동 차단)로 자연스럽게 이어집니다.

### STEP 1 — "안 새게 막는 규칙" 만들기
본인 레포 루트에 `.gitignore` 를 만들어 `.env` **와 그 변형까지** 막아 보세요.

```gitignore
.env
.env.*
!.env.example
*.pem
*.key
```

`docs/week01.md` 에 "왜 `.env.*` 까지 넣었는지" 한 줄 적기 (수업 실수 #4와 연결).

'*'는 보통 와일드카드임. '.env.아무문자' 와 같이 '.env.'로 시작하는 모든 파일을 해당시켜서 .env.local등의 파일도 커밋 푸시 무시할 수 있음

### STEP 2 — 실제로 막히는지 검증
직접 확인하고 결과(명령어 + 결과 한 줄)를 적으세요.

```bash
# 1) 새 .env 는 무시되는가?
echo "API_KEY=ghp_가짜키" > .env
git status            # → .env 가 안 보이면 성공

# 2) 변형 파일도 무시되는가?
echo "API_KEY=ghp_가짜키" > .env.local
git status            # → .env.local 도 안 보이면 성공


# 1~2 명령어 및 결과

echo "API_KEY=NOT_A_REAL_KEY_ENV_001" > .env
echo "API_KEY=NOT_A_REAL_KEY_LOCAL_002" > .env.local
echo "API_KEY=NOT_A_REAL_KEY_PRODUCTION_003" > .env.production
echo "API_KEY=replace_with_your_api_key" > .env.example
echo "THIS_IS_NOT_A_REAL_CERTIFICATE" > test.pem
echo "THIS_IS_NOT_A_REAL_PRIVATE_KEY" > test.key
➜  INHACK_Security_Development git:(main) ✗ git status
On branch main
Your branch is up to date with 'origin/main'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	.env.example
	.gitignore
	docs/

nothing added to commit but untracked files present (use "git add" to track)



# 3) '이미 추적 중이던' 파일은? (수업 실수 #3)
#    만약 예전에 실수로 .env 를 커밋했다면, .gitignore 만으로는 안 빠집니다.
git rm --cached .env  # 추적에서 제외 (파일은 로컬에 남음)
git status            # → 이제 삭제로 잡히고, 이후로는 무시됨

.gitignore에서 '.env.*'를 삭제한 후 커밋

```

> 정리: `.gitignore` 는 **아직 추적 안 하는 파일**만 막습니다.
> 이미 커밋된 시크릿은 `git rm --cached` 로 추적을 빼야 하고,
> **이미 새어나간 값은 반드시 폐기·교체**해야 합니다. (다음 주에 이걸 자동으로 잡는 도구를 씁니다.)