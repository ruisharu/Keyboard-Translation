입력한 문장을 특정 언어로 번역해 주는 프로그램입니다.

한국어, 영어, 일본어, 중국어 간체·번체, 스페인어를 양쪽 모두 지원합니다.
직접 번역 입력창도 제공합니다.

- Windows 11에서만 지원을 보장합니다. (이외의 운영체제는 작동을 보장하지 않습니다.)

1. 'KeyboardTranslationInstaller.exe' or 'KeyboardTranslation-0.1.8.exe'를 설치합니다.
2. [Python 3.12](https://www.python.org/downloads/windows/) 이상(3.14까지 지원) 또는 [uv](https://docs.astral.sh/uv/getting-started/installation/)를 설치합니다.
3. 저장소 폴더에서 PowerShell을 열고 실행합니다.

```powershell
uv sync --frozen
uv run --frozen python -m keyboard_translation
```

uv 없이 실행할 수도 있습니다.

```powershell
py -3.12 -m venv .venv
.\.venv\Scripts\python.exe -m pip install .
.\.venv\Scripts\python.exe -m keyboard_translation
```

4. [OpenAI API 사이트](https://platform.openai.com/api-keys)에 들어가서 API 키를 발급 받습니다.
5. 앱에 OpenAI API 키를 입력합니다. 기본 모델은 `gpt-4.1-mini`이며 설정에서 바꿀 수 있습니다.
5-1. **ChatGPT 구독과 API 결제는 별개**입니다. API 사용 가능한 키와 사용 한도가 필요하며, API 이용료는 유료입니다.
6. 입력·출력 언어를 고르고 설정을 저장합니다. 한국어, 일본어, 중국어 간체/번체, 스페인어를 지원하며, 기본은 한국어 → 일본어입니다.

기본 캡처는 **Shift+Home으로 선택되는 커서 앞의 현재 줄**입니다. 여러 줄이나 일부 문장만 번역하려면 캡처 방식을 `선택 영역`으로 바꾸고 텍스트를 직접 선택한 뒤 Ctrl+Q를 누르세요. Windows 접근성 기능으로 실제 선택 영역을 확인한 경우에만 자동으로 붙여넣습니다. 선택 영역을 확인할 수 없는 입력 칸은 번역 결과를 앱에 표시하며 직접 복사해 사용할 수 있습니다. 선택이 없을 때 줄 전체를 복사하는 편집기에서도 이를 선택 영역으로 간주해 자동 교체하지 않습니다.

## 키와 텍스트 처리

- API 키를 저장하면 Windows **DPAPI 사용자 암호화**를 적용한 파일로 보관합니다. 평문 키를 설정 JSON에 넣지 않습니다. 저장을 선택하지 않으면 해당 실행 동안만 사용합니다.
- `OPENAI_API_KEY` 환경변수도 지원합니다. 시작할 때 환경변수의 키가 있으면 저장된 키보다 우선 읽습니다. 키를 코드, Git, 채팅에 넣지 마세요.
- 설정 위치: `%LOCALAPPDATA%\KeyboardTranslation`. `settings.json`은 언어와 모델 설정이며 `api-key.dpapi`는 암호화된 키입니다. 키 파일은 해당 Windows 사용자 계정에서만 복호화할 수 있습니다.
- 번역할 때만 캡처한 문장을 OpenAI API로 전송합니다. 전역 키 입력을 기록하지 않으며 번역 기록을 디스크에 저장하지 않습니다. API 요청은 `store=False`를 사용합니다. 제공자의 별도 보관 정책까지 변경하는 설정은 아닙니다.
- 앱을 벗어나거나 입력을 이어가거나 클립보드를 바꾸면 자동 교체를 건너뛰고 앱에 결과를 남깁니다. 원문은 번역 성공 전 삭제하지 않습니다. 붙여넣기는 대상 앱의 Ctrl+V 지원에 따라 동작합니다.

## 지원 범위

Windows 11의 일반적인 선택·복사·붙여넣기 입력 칸을 대상으로 합니다. 줄 시작 동작은 편집기의 Home 키 처리와 줄바꿈 방식에 따릅니다. 일본어·한국어 IME 조합을 확정한 뒤 단축키를 사용하세요. 앱이 조합 확정용 Enter를 대신 누르지는 않습니다.

게임의 자체 입력 칸, 안티치트, 관리자 권한으로 실행된 앱, 암호 입력 칸 등은 캡처나 붙여넣기를 제한할 수 있습니다. 보호 기능을 우회하지 않습니다. 복사가 실패하면 원문을 유지하고 오류를 안내합니다. 기존 클립보드를 보관할 수 없는 특수 형식이나 32 MiB를 넘는 클립보드도 자동 캡처를 멈춥니다. 실제 게임별 호환성은 Windows에서 확인해야 합니다.

Linux/macOS에서는 GUI의 직접 번역을 사용할 수 있지만 전역 단축키와 DPAPI 키 저장을 지원하지 않습니다. 클라우드 Linux에서 Windows 전역 단축키·IME·게임 호환성을 확인한 것으로 간주하지 않습니다.

## 개발 및 테스트

```powershell
uv sync --frozen --extra dev
uv run --frozen --extra dev pytest
```

Linux GUI 스모크 테스트는 `QT_QPA_PLATFORM=offscreen`으로 실행합니다. 테스트는 API를 모킹하므로 실제 키나 요금이 필요하지 않습니다. 실제 API 품질·연결은 사용자의 API 키로 앱에서 확인해야 합니다.

실제 Windows 사용 전 [Windows 확인 절차](docs/windows-smoke-test.md)로 단축키, IME, 클립보드와 사용하는 게임의 입력 칸을 확인하세요. Windows에서 실행 파일을 만듭니다.

```powershell
pwsh -File scripts/build_windows.ps1
```

결과는 `dist\KeyboardTranslation.exe`입니다. 이 실행 파일에는 API 키를 포함하지 않습니다. Windows CI는 테스트 후 실행 파일을 아티팩트로 보관합니다. Linux에서 생성한 실행 파일은 Windows용이 아니므로 Windows 빌드를 사용하세요.
