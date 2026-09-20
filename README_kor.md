<h3 align="center">Pywr-next</h3>

<p align="center">
이 저장소는 <a href="https://github.com/pywr/pywr">Pywr</a>의 다음 메이저 버전을 위한 진행 중인 작업을 담고 있습니다.
Cython 대신 Rust를 백엔드로 사용합니다. 현재는 개발과 실험 용도 이외에는 사용할 준비가 되어 있지 않습니다.
의견과 토론을 환영합니다.
<br />
<br />
<a href="https://pywr.github.io/pywr-next/">사용자 가이드</a>
·
<a href="https://github.com/pywr/pywr-next/issues">버그 신고</a>
·
<a href="https://github.com/pywr/pywr-next/issues">기능 요청</a>
</p>

(영문 원본: `README.md`)

## 목차

- [프로젝트 소개](#프로젝트-소개)
  - [요구 사항](#요구-사항)
  - [사용 기술](#사용-기술)
- [시작하기](#시작하기)
- [사용법](#사용법)
- [Pywr v1.x 모델을 v2.x로 이식하기](#pywr-v1x-모델을-v2x로-이식하기)
- [크레이트](#크레이트)
- [로드맵](#로드맵)
- [기여하기](#기여하기)
- [라이선스](#라이선스)
- [연락처](#연락처)

## 프로젝트 소개

Pywr-1.x는 성능을 위해 Cython을 활용하는 Python 라이브러리입니다. 시간이 지나면서 최대 성능을 얻기 위해 Cython으로
작성된 "코어" 데이터 구조와 객체 집합이 생겨났습니다. Cython은 일반 Python으로 그 코어 기능을 쉽게 확장할 수 있다는
장점이 있습니다. 그러나 무엇이 Python이고 무엇이 Cython인지의 경계가 다소 모호하며, 일부는 잘 설계되어 있지 않습니다.

Pywr의 향후 개발(예: Pywr-2.x)을 위한 한 가지 선택지는 계산 "코어"와 상위 수준 기능을 더 명시적으로 분리하는 것입니다.
Rust는 Python과 거의 독립적으로 그 코어를 작성하기에 적합한 후보이며, (1) Cython보다 높은 성능과 (2) 향후 더 쉬운
유지보수라는 이점을 제공할 수 있습니다.

### 요구 사항

Pywr의 메이저 개정판은 다음 기능 요구 사항을 만족해야 합니다.

- Pywr-1.x의 "Parameter" 시스템 유지 – Pywr를 진정으로 유연하게 만드는 핵심 기능
- Python 공간에서 확장 가능
- 데이터와 지표 출력 방식 개선
- 더 나은 오류 처리
- 크로스 플랫폼
- 더 빠르게!
- 강력한 입력 파일(JSON) 스키마

### 사용 기술

- Rust
- Python

## 시작하기

### 사전 컴파일된 wheel 설치

[Pywr book](https://pywr.github.io/pywr-next/getting_started.html)의 안내를 참고하세요.

### 소스에서 컴파일

이 저장소는 Git 서브모듈로 Clp의 한 버전을 포함하고 있습니다. 빌드하려면 먼저 서브모듈을 초기화해야 합니다.

```bash
git submodule init
git submodule update
```

Python 확장을 설치하려면 Rust가 필요합니다. Python 개발 설치를 만들려면 먼저 Rust 라이브러리를 컴파일한 뒤 Python
확장을 컴파일해야 합니다. 다음 예시는 가상 환경을 사용해 Python 의존성을 설치하고, Pywr 확장을 컴파일한 다음, Pywr
Python CLI를 실행합니다.

```bash
python -m venv .venv # 새 가상 환경 생성
source .venv/bin/activate # 가상 환경 활성화 (linux)
# .venv\Scripts\activate # 가상 환경 활성화 (windows)
pip install maturin  # Python 확장 빌드용 maturin 설치
maturin develop # Pywr Python 확장 컴파일
python -m pywr  # Pywr Python CLI 실행
```

## 사용법

### Rust CLI

Python 없이 이 버전의 Pywr를 사용할 수 있도록 기본적인 명령줄 인터페이스가 포함되어 있습니다. 이 CLI는 `pywr-cli`
크레이트에 있습니다.

사용 가능한 CLI 명령을 보려면 다음을 실행하세요.

```bash
cargo run -p pywr-cli -- --help
```

Pywr v2 모델을 실행하려면 다음을 사용하세요.

```bash
cargo run -p pywr-cli -- run tests/models/simple1.json
```

### Python CLI

위 안내대로 Python 확장을 컴파일했다면 기본 Python CLI로 모델을 실행할 수 있습니다.

```bash
python -m pywr run tests/models/simple1.json
```

## Pywr v1.x 모델을 v2.x로 이식하기

이 버전의 Pywr는 Pywr v1.x와 하위 호환되지 않습니다. 이 버전을 만든 주요 이유 중 하나가 Pywr v1.x JSON 파일에 강력한
스키마가 없다는 점입니다. Pywr v2.x는 이 저장소에 정의된 갱신된 JSON 스키마를 사용합니다. 따라서 v1.x JSON 파일은
v2.x JSON 스키마로 변환해야 합니다. 이 변환은 수동으로 할 수도 있지만, 진행 중인 변환 도구도 있습니다. 변환 도구는
[pywr-schema](https://github.com/pywr/pywr-schema) 프로젝트에 정의된 v1.x 스키마를 사용합니다.

**Pywr v1.x에서 v2.x로의 변환은 실험적이며, Pywr의 모든 기능이 `pywr-schema`에 구현되어 있거나 Pywr v2.x에 구현된
것은 아닙니다. 두 버전 사이의 변경 때문에 자동 변환이 모델을 완전히 변환하지 못할 가능성이 매우 높으며, 반드시
수동 테스트와 확인이 _필요합니다_.**

```bash
cargo run --no-default-features -- convert /path/to/my/v1.x/model.json
```

모델 이식에 대한 피드백을 매우 환영하니, 질문이나 문제가 있으면 이슈를 열어 주세요.

## 크레이트

이 저장소에는 다음 크레이트가 포함되어 있습니다.

### Pywr-core

네트워크 모델을 구성하기 위한 저수준 Rust 라이브러리입니다. 이 크레이트가 선형계획 솔버와 연동됩니다.

Feature 플래그:

| Feature    | 설명                                          | 기본값 |
|------------|-----------------------------------------------|--------|
| `pyo3`     | Python 바인딩 활성화                          | True   |
| `highs`    | HiGHS LP 솔버 활성화                          | False  |
| `ipm-ocl`  | OpenCL IPM 솔버 활성화 (nightly 필요)         | False  |
| `ipm-simd` | AVX IPM 솔버 활성화 (nightly 필요)            | False  |
| `cbc`      | CBC MILP 솔버 활성화                          | False  |

### Pywr-schema

Pywr JSON 파일을 스키마로 검증하고, 그 스키마로부터 `pywr-core`를 사용해 모델을 빌드하는 Rust 라이브러리입니다.

Feature 플래그:

| Feature    | 설명                                                                                                                                                                  | 기본값 |
|------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| `core`     | `pywr-core`로 스키마에서 모델 빌드를 활성화. 기본으로 켜져 있지만 많은 의존성이 필요함. 스키마 검증과 조작만 필요하다면 `default-features = false`로 빌드하는 것을 고려 | True   |
| `pyo3`     | Python 바인딩 활성화                                                                                                                                                  | True   |
| `highs`    | HiGHS LP 솔버 활성화                                                                                                                                                  | False  |
| `ipm-ocl`  | OpenCL IPM 솔버 활성화 (nightly 필요)                                                                                                                                 | False  |
| `ipm-simd` | AVX IPM 솔버 활성화 (nightly 필요)                                                                                                                                    | False  |
| `cbc`      | CBC MILP 솔버 활성화                                                                                                                                                  | False  |

### Pywr-cli

Pywr 모델을 실행하기 위한 명령줄 인터페이스입니다.

### Pywr-python

Pywr 모델을 구성하고 실행하기 위한 Python 확장(및 패키지)입니다.

## 로드맵

- [x] 개념 증명 – RIIR(Rust로 다시 작성) 접근의 이점 입증
- [ ] 출력 및 지표 재설계
- [ ] 외부 최적화 알고리즘과의 통합을 위한 변수 API 재설계
- [ ] Pywr v1.x의 남은 `Parameters` 구현
- [ ] Rust 확장을 사용한 Python API 설계 및 구현
- [ ] Pywr v2.x 베타 릴리스

제안된 기능(및 알려진 문제)의 전체 목록은 [열린 이슈](https://github.com/pywr/pywr-next/issues)를 참고하세요.

## 기여하기

기여는 오픈 소스 커뮤니티를 배우고, 영감을 얻고, 창작하는 놀라운 공간으로 만드는 원동력입니다. 어떤 기여든 **매우
감사히** 받겠습니다.

개선 제안이 있다면 저장소를 포크하고 pull request를 만들어 주세요. "enhancement" 태그로 이슈를 열어도 됩니다.
프로젝트에 스타를 남기는 것도 잊지 마세요! 감사합니다!

1. 프로젝트를 포크합니다
2. 기능 브랜치를 만듭니다 (`git checkout -b feature/AmazingFeature`)
3. 변경 사항을 커밋합니다 (`git commit -m 'Add some AmazingFeature'`)
4. 브랜치에 푸시합니다 (`git push origin feature/AmazingFeature`)
5. Pull Request를 엽니다

## 라이선스

Apache 2.0 또는 MIT 라이선스로 배포됩니다. 자세한 내용은 `LICENSE-APACHE`와 `LICENSE-MIT`를 참고하세요.

## 연락처

James Tomlinson - tomo.bbe@gmail.com

프로젝트 링크: [https://github.com/pywr/pywr-next](https://github.com/pywr/pywr-next)
