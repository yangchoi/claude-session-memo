---
name: memo
description: 세션 간 메모 공유. "/memo save 내용", "/memo list", "/memo clear" 사용
---

# Session Memo

세션 간 메모를 공유하여 병렬 작업 시 handoff를 쉽게 합니다.

## 사용법

### 메모 저장
```
/memo save <내용>
/memo save User API 완성: POST /api/users/verify
```

### 메모 목록
```
/memo list
/memo
```

### 메모 삭제
```
/memo clear        # 전체 삭제
/memo delete <id>  # 특정 메모 삭제
```

## 구현

메모 파일 위치: `~/.claude/session-memos.json`

### Step 1: 인자 파싱

사용자 입력에서 subcommand와 내용을 파싱합니다:
- `save <내용>`: 새 메모 저장
- `list` 또는 인자 없음: 메모 목록 표시
- `clear`: 전체 삭제
- `delete <id>`: 특정 메모 삭제

### Step 2: 메모 파일 처리

```bash
MEMO_FILE=~/.claude/session-memos.json

# 파일이 없으면 생성
if [ ! -f "$MEMO_FILE" ]; then
  echo "[]" > "$MEMO_FILE"
fi
```

### Step 3: 명령 실행

**save**: 새 메모를 타임스탬프와 함께 저장
```bash
# JSON에 새 메모 추가
jq --arg memo "$CONTENT" --arg time "$(date -Iseconds)" \
  '. += [{"id": (. | length + 1), "memo": $memo, "time": $time}]' \
  "$MEMO_FILE" > tmp && mv tmp "$MEMO_FILE"
```

**list**: 저장된 메모 목록 표시
```bash
jq -r '.[] | "[\(.id)] \(.time | split("T")[0]) - \(.memo)"' "$MEMO_FILE"
```

**clear**: 전체 삭제
```bash
echo "[]" > "$MEMO_FILE"
```

**delete**: 특정 ID 삭제
```bash
jq --argjson id "$ID" 'del(.[] | select(.id == $id))' "$MEMO_FILE" > tmp && mv tmp "$MEMO_FILE"
```

### Step 4: 결과 출력

작업 완료 후 적절한 메시지를 출력합니다.

## 예시

**세션 A에서:**
```
/memo save Backend API 완성: POST /api/users/verify, OAuth 인증 추가됨
```

**세션 B에서:**
```
/memo list
# 출력:
# [1] 2024-02-11 - Backend API 완성: POST /api/users/verify, OAuth 인증 추가됨
```

## 팁

- 작업 완료 시 다음 세션에서 이어받을 내용을 메모
- API 엔드포인트, 변경된 파일, 주의사항 등 기록
- 정기적으로 `/memo clear`로 오래된 메모 정리
