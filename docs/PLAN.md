# docsort 구현 계획

## Phase 1: 프로젝트 기반 구축

### 1.1 Go 모듈 초기화

- [ ] `go mod init` 실행
- [ ] 프로젝트 디렉토리 구조 설계

```text
docsort/
├── cmd/
│   └── docsort/
│       └── main.go
├── internal/
│   ├── config/       # 설정 파일 처리
│   ├── embedding/    # 임베딩 모델
│   ├── clustering/   # 군집화 로직
│   ├── llm/          # LLM 연동
│   ├── organizer/    # 파일 정리 로직
│   └── glossary/     # 용어집 관리
├── .docsort.yaml
├── go.mod
└── go.sum
```

### 1.2 설정 파일 구현

- [ ] `.docsort.yaml` 스키마 정의
- [ ] 설정 로드/저장 기능 구현 (goccy/go-yaml 사용)
- [ ] 기본값 설정

```yaml
# .docsort.yaml 예시
embedding:
  provider: "local"  # local | openai
  model: "all-MiniLM-L6-v2"
  api_key: ""

llm:
  provider: "openai"
  model: "gpt-4o-mini"
  api_key: ""

glossary:
  path: "glossary.txt"

clustering:
  min_cluster_size: 3
  max_depth: 3
```

---

## Phase 2: CLI 스켈레톤

### 2.1 CLI 프레임워크 설정

- [ ] kong 라이브러리 설정 (구조체 태그 기반)
- [ ] CLI 구조체 정의
- [ ] 서브커맨드 스켈레톤 생성
  - [ ] `init`
  - [ ] `organize`
  - [ ] `add`

```go
// CLI 구조체 예시
type CLI struct {
    Init     InitCmd     `cmd:"" help:"용어집 생성"`
    Organize OrganizeCmd `cmd:"" help:"전체 문서 정리"`
    Add      AddCmd      `cmd:"" help:"새 파일 추가"`

    Config  string `short:"c" help:"설정 파일 경로" default:".docsort.yaml"`
    Verbose bool   `short:"v" help:"상세 출력"`
}
```

### 2.2 플래그 정의

- [ ] `organize --fresh` / `--preserve`
- [ ] `organize --dry-run`
- [ ] `add --dry-run`
- [ ] 전역 플래그: `--config`, `--verbose`

---

## Phase 3: 파일 처리 기능

### 3.1 파일 스캐너

- [ ] 디렉토리 재귀 탐색
- [ ] Markdown 파일 필터링 (`.md` 확장자)
- [ ] 파일 메타데이터 수집 (경로, 이름, 크기)

### 3.2 파일 내용 처리

- [ ] Markdown 파일 읽기
- [ ] 제목/헤더 추출
- [ ] 본문 텍스트 추출

### 3.3 파일 이동

- [ ] 디렉토리 생성
- [ ] 파일 이동/복사
- [ ] dry-run 모드 (실제 이동 없이 계획만 출력)

---

## Phase 4: 임베딩 시스템

### 4.1 임베딩 인터페이스 정의

- [ ] `Embedder` 인터페이스 설계

```go
type Embedder interface {
    Embed(text string) ([]float64, error)
    EmbedBatch(texts []string) ([][]float64, error)
}
```

### 4.2 로컬 임베딩 구현 (ollama)

- [ ] ollama HTTP API 클라이언트 구현
- [ ] 한국어 지원 모델 선택 (nomic-embed 등)
- [ ] 배치 처리

### 4.3 외부 API 임베딩 구현

- [ ] OpenAI Embedding API 연동 (go-openai 사용)
- [ ] 배치 처리 (비용 최적화)
- [ ] 에러 핸들링 및 재시도

---

## Phase 5: 군집화 시스템

### 5.1 벡터 연산

- [ ] 코사인 유사도 계산
- [ ] 유사도 행렬 생성

### 5.2 클러스터링 알고리즘 (직접 구현)

- [ ] 계층적 클러스터링 (Hierarchical Clustering) 구현
- [ ] 클러스터 수 자동 결정 (실루엣 스코어 등)
- [ ] 계층 깊이 제한

### 5.3 계층 구조 생성

- [ ] 클러스터 → 폴더 구조 변환
- [ ] 중첩 클러스터 처리

---

## Phase 6: LLM 연동

### 6.1 LLM 인터페이스 정의

- [ ] `LLM` 인터페이스 설계

```go
type LLM interface {
    GenerateFolderName(documents []Document, glossary []string) (string, error)
}
```

### 6.2 LLM 프로바이더 구현

- [ ] OpenAI API 연동 (go-openai 사용)
- [ ] 프롬프트 템플릿 설계
- [ ] 용어집 참조 로직

### 6.3 폴더 이름 생성

- [ ] 클러스터 대표 문서 선택
- [ ] 계층별 이름 생성
- [ ] 용어집 기반 이름 정규화

---

## Phase 7: 핵심 커맨드 구현

### 7.1 `init` 커맨드

- [ ] 기존 문서 스캔
- [ ] 클러스터링 실행
- [ ] LLM으로 용어집 초안 생성
- [ ] `glossary.txt` 저장
- [ ] 사용자 편집 안내

### 7.2 `organize` 커맨드

- [ ] 전체 파일 스캔
- [ ] 임베딩 생성
- [ ] 클러스터링
- [ ] 폴더 구조 결정
- [ ] `--fresh` 모드: 기존 구조 무시
- [ ] `--preserve` 모드: 기존 구조 최대한 유지
- [ ] `--dry-run` 모드: 계획만 출력
- [ ] 실제 파일 이동

### 7.3 `add` 커맨드

- [ ] 단일 파일 임베딩
- [ ] 기존 클러스터와 유사도 비교
- [ ] 최적 위치 결정
- [ ] `--dry-run` 모드
- [ ] 파일 이동

---

## Phase 8: 테스트

### 8.1 단위 테스트

- [ ] 설정 파일 로드/저장
- [ ] 파일 스캐너
- [ ] 임베딩 (모킹)
- [ ] 클러스터링
- [ ] 파일 이동

### 8.2 통합 테스트

- [ ] 전체 파이프라인 테스트
- [ ] dry-run 모드 검증
- [ ] 대용량 파일 테스트 (2000개)

---

## Phase 9: 마무리

### 9.1 최적화

- [ ] 임베딩 캐싱 (파일 해시 기반)
- [ ] 병렬 처리
- [ ] 메모리 사용량 최적화

### 9.2 사용자 경험

- [ ] 진행률 표시 (progressbar 사용)
- [ ] 에러 메시지 개선
- [ ] 도움말 문서

---

## 구현 우선순위

```text
높음 ████████████████████
     Phase 1 → Phase 2 → Phase 3

중간 ████████████████
     Phase 4 → Phase 5 → Phase 7

낮음 ████████████
     Phase 6 → Phase 8 → Phase 9
```

### 권장 순서

1. **Phase 1**: 프로젝트 기반 (필수)
2. **Phase 2**: CLI 스켈레톤 (필수)
3. **Phase 3**: 파일 처리 (필수)
4. **Phase 4**: 임베딩 - 외부 API 먼저 (빠른 프로토타이핑)
5. **Phase 5**: 군집화 (핵심 로직)
6. **Phase 7**: 커맨드 구현 - `organize` 먼저
7. **Phase 6**: LLM 연동 (폴더 이름 생성)
8. **Phase 7**: 나머지 커맨드 (`init`, `add`)
9. **Phase 4**: 로컬 임베딩 추가 (ollama)
10. **Phase 8**: 테스트
11. **Phase 9**: 마무리

---

## 선택된 의존성 (Go 패키지)

| 영역 | 패키지 |
| ------ | -------- |
| CLI | <https://github.com/alecthomas/kong> |
| YAML | <https://github.com/goccy/go-yaml> |
| LLM/임베딩 API | <https://github.com/sashabaranov/go-openai> |
| 수학 연산 | <https://github.com/gonum/gonum> |
| 진행률 | <https://github.com/schollz/progressbar> |

```go
// go.mod 예상
require (
    github.com/alecthomas/kong v0.9.0
    github.com/goccy/go-yaml v1.15.0
    github.com/sashabaranov/go-openai v1.24.0
    gonum.org/v1/gonum v0.15.0
    github.com/schollz/progressbar/v3 v3.14.0
)
```

---

## 마일스톤

| 마일스톤      | 포함 Phase   | 결과물                    |
| ------------- | ------------ | ------------------------- |
| M1: 기본 동작 | 1, 2, 3      | 파일 스캔 및 이동 가능    |
| M2: 자동 분류 | 4, 5         | 임베딩 + 군집화 동작      |
| M3: MVP       | 7 (organize) | `organize --dry-run` 동작 |
| M4: 완성      | 6, 7, 8      | 전체 기능 완성            |
| M5: 안정화    | 9            | 최적화 및 문서화          |
