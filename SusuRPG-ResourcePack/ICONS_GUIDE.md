# icons.png 투명화 가이드

## 📝 작업 순서

### 1. 바닐라 리소스 추출

**Minecraft 버전**: 1.20.1

**파일 위치**:
```
Windows: %appdata%\.minecraft\versions\1.20.1\1.20.1.jar
Mac: ~/Library/Application Support/minecraft/versions/1.20.1/1.20.1.jar
Linux: ~/.minecraft/versions/1.20.1/1.20.1.jar
```

**추출 방법**:
```bash
# JAR 파일을 압축 프로그램으로 열기
# assets/minecraft/textures/gui/icons.png 찾기
# 복사하여 작업 폴더에 저장
```

---

## 🎨 편집 방법

### Paint.NET 사용 (Windows)

1. **icons.png 열기**
   - 파일 > 열기
   - icons.png 선택

2. **투명도 활성화**
   - 레이어 > 이미지 크기
   - 256x256 확인

3. **하트 영역 투명화**
   ```
   도구 > 사각형 선택
   
   위치 지정:
   - X: 16, Y: 0, Width: 90, Height: 18
   
   Delete 키 누르기
   ```

4. **배고픔 영역 투명화**
   ```
   도구 > 사각형 선택
   
   위치 지정:
   - X: 16, Y: 27, Width: 90, Height: 18
   
   Delete 키 누르기
   ```

5. **저장**
   - 파일 > 다른 이름으로 저장
   - PNG 형식
   - 투명도 유지 체크

---

### GIMP 사용 (크로스 플랫폼)

1. **icons.png 열기**

2. **투명 레이어 추가**
   - 레이어 > 투명도 > 알파 채널 추가

3. **하트 영역 선택 및 삭제**
   ```
   도구 > 사각형 선택
   위치: 16, 0
   크기: 90 x 18
   편집 > 지우기
   ```

4. **배고픔 영역 선택 및 삭제**
   ```
   도구 > 사각형 선택
   위치: 16, 27
   크기: 90 x 18
   편집 > 지우기
   ```

5. **저장**
   - 파일 > Export As
   - icons.png
   - PNG 옵션에서 투명도 유지

---

## 📐 정확한 좌표

### 하트 (Hearts)

```
전체 하트 영역: (16, 0) ~ (105, 18)

상세:
- 빈 하트 (empty): (16, 0) ~ (25, 9)
- 반 하트 (half): (61, 0) ~ (70, 9)
- 전체 하트 (full): (52, 0) ~ (61, 9)
- 하드코어 하트: (160, 0) ~ (169, 9)
- 독 하트: (88, 0) ~ (97, 9)
- 시들은 하트: (16, 9) ~ (25, 18)
- 흡수 하트: (160, 0) ~ (169, 9)
- 냉기 하트: (144, 0) ~ (153, 9)
```

### 배고픔 (Food)

```
전체 배고픔 영역: (16, 27) ~ (105, 45)

상세:
- 빈 배고픔 (empty): (16, 27) ~ (25, 36)
- 반 배고픔 (half): (61, 27) ~ (70, 36)
- 전체 배고픔 (full): (52, 27) ~ (61, 36)
- 배고픔 (hungry): (133, 27) ~ (142, 36)
```

---

## 🖼️ 간단한 방법: 미리 만든 파일 사용

**투명 icons.png 다운로드**:

아래 내용을 참고하여 직접 편집하거나, 온라인 편집기 사용:

### 온라인 편집기

1. **Photopea** (https://www.photopea.com)
   - 무료 온라인 포토샵
   - icons.png 업로드
   - 사각형 선택 도구로 영역 선택
   - Delete
   - Export PNG

2. **Pixlr** (https://pixlr.com)
   - 무료 온라인 편집기
   - 동일한 방법

---

## 📦 리소스팩 패키징

### 폴더 구조

```
SusuRPG-HUD/
├── pack.mcmeta
├── pack.png (선택사항)
└── assets/
    └── minecraft/
        └── textures/
            └── gui/
                └── icons.png (편집된 버전)
```

### pack.mcmeta 내용

```json
{
  "pack": {
    "pack_format": 15,
    "description": "§bSusuRPG Custom HUD\n§7하트/배고픔 숨김"
  }
}
```

### 압축

```
1. SusuRPG-HUD 폴더 우클릭
2. 압축 > ZIP 파일로
3. SusuRPG-HUD.zip 생성
```

---

## 🚀 적용 방법

### 로컬 적용 (클라이언트)

```
1. SusuRPG-HUD.zip 파일 복사
2. .minecraft/resourcepacks/ 폴더에 붙여넣기
3. Minecraft 실행
4. 옵션 > 리소스 팩
5. SusuRPG-HUD 선택 > 완료
```

### 서버 강제 적용

**server.properties 편집**:

```properties
resource-pack=https://your-server.com/downloads/SusuRPG-HUD.zip
resource-pack-sha1=<파일의 SHA1 해시>
require-resource-pack=true
resource-pack-prompt=§bSusuRPG §7커스텀 HUD를 사용하려면\n리소스팩을 적용해주세요!
```

**SHA1 해시 생성**:

```bash
# Linux/Mac
sha1sum SusuRPG-HUD.zip

# Windows (PowerShell)
Get-FileHash SusuRPG-HUD.zip -Algorithm SHA1
```

---

## ✅ 확인 방법

### 성공 시

```
[게임 화면]
- 하트가 보이지 않음 (투명)
- 배고픔이 보이지 않음 (투명)
- ActionBar에 커스텀 HUD 표시:
  ❤❤❤❤❤♡♡♡♡♡ 10   ⚡⚡⚡⚡⚡⚐⚐⚐⚐⚐   ◆◆◆◆◆◇◇◇◇◇ 10
```

### 실패 시

```
- 하트가 여전히 보임 → icons.png가 제대로 적용 안 됨
- 배고픔이 여전히 보임 → 투명화 실패
```

**해결 방법**:
1. 리소스팩 다시 확인
2. F3+T 눌러서 리소스팩 리로드
3. 게임 재시작

---

## 🎯 최종 결과

### 화면 레이아웃

```
┌─────────────────────────────────────┐
│                                     │
│              게임 화면               │
│         (하트/배고픔 안 보임)         │
│                                     │
│                                     │
│                                     │
│                                     │
│                                     │
│                                     │
├─────────────────────────────────────┤
│ ❤❤❤❤❤♡♡♡♡♡ 10  ⚡⚡⚡⚡⚡⚐⚐⚐⚐⚐  ◆◆◆◆◆◇◇◇◇◇ 10 │ ← ActionBar
├─────────────────────────────────────┤
│ [1] [2] [3] [4] [5] [6] [7] [8] [9]│
└─────────────────────────────────────┘
```

---

**리소스팩 제작이 완료되면 압축하여 적용하세요!** 🎨
