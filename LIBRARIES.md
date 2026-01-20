# docsort 라이브러리 선택 가이드

## 1. CLI 프레임워크

### Option A: cobra
```
github.com/spf13/cobra
```
| 장점 | 단점 |
|------|------|
| Go CLI의 사실상 표준 | 상대적으로 무거움 |
| 풍부한 기능 (자동완성, 도움말) | 학습 곡선 존재 |
| 서브커맨드 지원 우수 | 의존성 많음 |
| 대규모 커뮤니티, 문서 풍부 | **공식 예제가 global 변수 패턴 사용** |
| kubectl, hugo, gh 등에서 사용 | 테스트하기 어려운 구조 유도 |

### Option B: urfave/cli
```
github.com/urfave/cli/v2
```
| 장점 | 단점 |
|------|------|
| cobra보다 가벼움 | cobra보다 기능 적음 |
| 직관적인 API | 자동완성 설정 복잡 |
| 빠른 시작 가능 | |

### Option C: kong ✅ 선택
```
github.com/alecthomas/kong
```
| 장점 | 단점 |
|------|------|
| 구조체 태그 기반 선언적 정의 | 커뮤니티 작음 |
| 코드량 최소화 | 복잡한 케이스에서 제한적 |
| 타입 안전성 | |
| **global 변수 없이 사용 가능** | |
| 테스트 용이한 구조 | |
| 의존성 주입 친화적 | |

### Option D: 표준 라이브러리 (flag)
```
flag (내장)
```
| 장점 | 단점 |
|------|------|
| 외부 의존성 없음 | 서브커맨드 직접 구현 필요 |
| 가장 가벼움 | 기능 제한적 |
| | 도움말 커스터마이징 어려움 |

**선택: kong** - global 변수 없이 구조체 기반으로 깔끔한 설계 가능, 테스트 용이

---

## 2. 설정 파일 관리

### Option A: viper
```
github.com/spf13/viper
```
| 장점 | 단점 |
|------|------|
| YAML, JSON, TOML 등 다양한 형식 | 무거움 |
| 환경변수 자동 바인딩 | 리플렉션 많이 사용 |
| cobra와 통합 용이 | |
| 핫 리로드 지원 | |

### Option B: koanf
```
github.com/knadh/koanf
```
| 장점 | 단점 |
|------|------|
| viper보다 가벼움 | viper보다 커뮤니티 작음 |
| 모듈화된 설계 | |
| 필요한 기능만 선택 가능 | |

### Option C: yaml.v3 직접 사용 ⚠️ ARCHIVED
```
gopkg.in/yaml.v3
```
| 장점 | 단점 |
|------|------|
| 최소 의존성 | **프로젝트 archived, 유지보수 중단** |
| 완전한 제어 | 환경변수 바인딩 직접 구현 |
| | 보일러플레이트 많음 |

---

## 2-1. YAML 라이브러리 (상세)

> ⚠️ `gopkg.in/yaml.v3` (go-yaml/yaml)가 archived 됨. 대안 필요.

### Option A: go.yaml.in/yaml/v3
```
go.yaml.in/yaml/v3
```
| 장점 | 단점 |
|------|------|
| yaml.org 공식 관리 포크 | 아직 마이그레이션 진행 중인 생태계 |
| gopkg.in/yaml.v3와 API 호환 | 일부 프로젝트에서 타입 충돌 가능 |
| 기존 코드 마이그레이션 용이 | |
| 버그 수정 포함 | |
| 안정적인 변경 (과격한 변경 없음) | |

### Option B: goccy/go-yaml ✅ 선택
```
github.com/goccy/go-yaml
```
| 장점 | 단점 |
|------|------|
| **릴리즈 활발, 최신 유지보수** | API가 yaml.v3와 다름 |
| Go-idiomatic 재구현 | 마이그레이션 코드 수정 필요 |
| YAML 테스트 스위트 355/402 통과 (yaml.v3는 295) | |
| 다수 메인테이너, 팀 기반 개발 | |
| 코드 가독성 좋음 | |
| 더 정확한 YAML 파싱 | |

### Option C: sigs.k8s.io/yaml
```
sigs.k8s.io/yaml
```
| 장점 | 단점 |
|------|------|
| Kubernetes 생태계 표준 | YAML↔JSON 변환 중심 |
| go.yaml.in/yaml/v3 기반 | 순수 YAML 용도에는 과함 |
| 타입 별칭 제공 | |

### YAML 라이브러리 비교

| 라이브러리 | 상태 | API 호환성 | 테스트 통과율 |
|-----------|------|-----------|--------------|
| gopkg.in/yaml.v3 | ❌ archived | 기준 | 295/402 |
| go.yaml.in/yaml/v3 | ✅ 유지보수 | 호환 | 295/402+ |
| goccy/go-yaml | ✅ 활발 | 비호환 | 355/402 |

**선택: goccy/go-yaml** - 활발한 릴리즈, 최신 유지보수, 더 정확한 파싱

---

## 3. OpenAI / LLM API

### Option A: go-openai
```
github.com/sashabaranov/go-openai
```
| 장점 | 단점 |
|------|------|
| OpenAI 전용, 완성도 높음 | OpenAI만 지원 |
| 스트리밍, 임베딩 모두 지원 | |
| 활발한 유지보수 | |
| 타입 정의 완벽 | |

### Option B: go-anthropic
```
github.com/liushuangls/go-anthropic
```
| 장점 | 단점 |
|------|------|
| Claude API 지원 | Anthropic만 지원 |
| 최신 API 반영 | |

### Option C: langchaingo
```
github.com/tmc/langchaingo
```
| 장점 | 단점 |
|------|------|
| 여러 LLM 프로바이더 통합 | 추상화로 인한 복잡성 |
| 체인, 에이전트 등 고급 기능 | 오버엔지니어링 가능성 |
| Python LangChain과 유사 | 아직 성숙도 낮음 |

### Option D: HTTP 직접 호출
```
net/http (내장)
```
| 장점 | 단점 |
|------|------|
| 의존성 없음 | 모든 것 직접 구현 |
| 완전한 제어 | 에러 핸들링 직접 구현 |
| | 타입 정의 직접 작성 |

**권장: go-openai** - 임베딩 + LLM 모두 필요하고, 안정적

---

## 4. 임베딩 (로컬)

### Option A: go-sentence-transformers (ONNX 기반)
```
github.com/AniketSindhu/go-sentence-transformers
```
| 장점 | 단점 |
|------|------|
| sentence-transformers 모델 사용 가능 | 유지보수 불확실 |
| ONNX 런타임 기반 | 설정 복잡 |
| 다양한 모델 지원 | |

### Option B: onnxruntime-go
```
github.com/yalue/onnxruntime_go
```
| 장점 | 단점 |
|------|------|
| ONNX 모델 직접 실행 | 모델 로딩 직접 구현 |
| 성능 좋음 | 토크나이저 별도 필요 |
| GPU 지원 (옵션) | 복잡도 높음 |

### Option C: ollama API
```
HTTP API 호출 (localhost)
```
| 장점 | 단점 |
|------|------|
| 설치만 하면 바로 사용 | ollama 설치 필요 |
| 다양한 모델 지원 | 외부 프로세스 의존 |
| 임베딩 모델 지원 (nomic-embed) | |
| Go 코드 단순화 | |

### Option D: 외부 서비스로 대체
```
OpenAI Embedding API 사용
```
| 장점 | 단점 |
|------|------|
| 구현 가장 간단 | 비용 발생 |
| 품질 보장 | 네트워크 필요 |
| | 프라이버시 우려 |

**권장: ollama API** - 로컬 실행 + 구현 단순화 균형

---

## 5. 벡터 연산 / 수학

### Option A: gonum
```
gonum.org/v1/gonum
```
| 장점 | 단점 |
|------|------|
| Go 수학 라이브러리 표준 | 학습 곡선 |
| 행렬 연산, 통계, 클러스터링 | API가 numpy보다 복잡 |
| 최적화된 성능 | |
| 순수 Go (cgo 없음 옵션) | |

### Option B: 직접 구현
```
math (내장)
```
| 장점 | 단점 |
|------|------|
| 의존성 없음 | 코사인 유사도 정도만 실용적 |
| 단순한 연산에 적합 | 클러스터링은 직접 구현 필요 |

### Option C: gorgonia
```
gorgonia.org/gorgonia
```
| 장점 | 단점 |
|------|------|
| 딥러닝 프레임워크 | 이 프로젝트에 과함 |
| GPU 지원 | 복잡함 |
| 자동 미분 | |

**권장: gonum** - 클러스터링 알고리즘에 필수

---

## 6. 클러스터링 알고리즘

### Option A: gonum/stat/cluster (없음 - 직접 구현 필요)
gonum에 클러스터링이 내장되어 있지 않음

### Option B: 직접 구현
| 알고리즘 | 장점 | 단점 |
|---------|------|------|
| K-Means | 구현 간단, 빠름 | K 미리 지정 필요 |
| Hierarchical (계층적) | K 불필요, 덴드로그램 | 느림 (O(n²) 이상) |
| DBSCAN | K 불필요, 노이즈 처리 | 파라미터 튜닝 필요 |
| HDBSCAN | DBSCAN 개선 | 구현 복잡 |

### Option C: 외부 호출 (Python)
```
exec.Command("python", "cluster.py", ...)
```
| 장점 | 단점 |
|------|------|
| scikit-learn 사용 가능 | Python 의존성 |
| 검증된 알고리즘 | IPC 오버헤드 |
| | 배포 복잡 |

**권장: Hierarchical 클러스터링 직접 구현** - 계층 구조 생성에 적합

---

## 7. 파일 시스템

### Option A: 표준 라이브러리
```
os, path/filepath, io/fs (내장)
```
| 장점 | 단점 |
|------|------|
| 의존성 없음 | 고급 기능 직접 구현 |
| 충분한 기능 | |

### Option B: afero
```
github.com/spf13/afero
```
| 장점 | 단점 |
|------|------|
| 파일시스템 추상화 | 이 프로젝트에 과함 |
| 테스트 용이 (메모리 FS) | |
| 다양한 백엔드 | |

**권장: 표준 라이브러리** - 충분함

---

## 8. 진행률 표시

### Option A: progressbar
```
github.com/schollz/progressbar/v3
```
| 장점 | 단점 |
|------|------|
| 간단한 API | 기능 제한적 |
| 가벼움 | |

### Option B: mpb
```
github.com/vbauerster/mpb/v8
```
| 장점 | 단점 |
|------|------|
| 다중 프로그레스바 | 약간 복잡 |
| 커스터마이징 풍부 | |
| 병렬 작업에 적합 | |

### Option C: 직접 구현
```
fmt.Printf("\r진행: %d/%d", current, total)
```
| 장점 | 단점 |
|------|------|
| 의존성 없음 | 기능 제한적 |
| 단순 | 터미널 호환성 문제 가능 |

**권장: progressbar** - 단순하고 충분함

---

## 최종 선택 조합

| 영역 | 라이브러리 | 이유 |
|------|-----------|------|
| CLI | **kong** | global 변수 없음, 테스트 용이 |
| 설정/YAML | **goccy/go-yaml** | 활발한 유지보수, 정확한 파싱 |
| LLM API | **go-openai** | 임베딩 + LLM 통합 |
| 로컬 임베딩 | **ollama API** | 구현 단순화 |
| 수학 연산 | **gonum** | 행렬 연산 필수 |
| 클러스터링 | **직접 구현** | 맞춤 필요 |
| 파일 처리 | **표준 라이브러리** | 충분함 |
| 진행률 | **progressbar** | 단순함 |

---

## 의존성 요약

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

## 대안 조합 (미니멀)

의존성을 최소화하고 싶다면:

| 영역 | 라이브러리 |
|------|-----------|
| CLI | **urfave/cli** |
| 설정 | **goccy/go-yaml** 직접 |
| LLM API | **HTTP 직접** |
| 임베딩 | **OpenAI API만** |
| 수학 | **직접 구현** |
| 클러스터링 | **직접 구현** |
| 진행률 | **직접 구현** |

장점: 외부 의존성 2-3개로 축소
단점: 보일러플레이트 증가, 개발 시간 증가
