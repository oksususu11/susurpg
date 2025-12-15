# SusuRPG 커스텀 HUD 리소스팩 가이드

## 📦 리소스팩 구조

```
SusuRPG-ResourcePack/
├── pack.mcmeta                     # 리소스팩 정보
├── pack.png                        # 리소스팩 아이콘 (128x128)
└── assets/
    └── minecraft/
        └── textures/
            └── gui/
                ├── icons.png       # 하트, 배고픔 등 UI 요소 (투명화)
                └── bars.png        # BossBar 텍스처 (투명화)
```

---

## 🎨 필요한 작업

### 1. 기존 UI 제거

#### icons.png 수정
**파일 경로**: `assets/minecraft/textures/gui/icons.png`

**크기**: 256x256 픽셀

**수정할 부분**:
```
- 하트 (체력): 투명하게 만들기
- 배고픔: 투명하게 만들기
- 갑옷: 그대로 유지
- 공기 방울: 그대로 유지
```

#### 좌표 정보
```
하트 (Heart):
  - 빈 하트: (16, 0) ~ (25, 9)
  - 반 하트: (61, 0) ~ (70, 9)
  - 전체 하트: (52, 0) ~ (61, 9)
  - 독 하트: (88, 0) ~ (97, 9)
  - 시들은 하트: (16, 9) ~ (25, 18)
  
배고픔 (Food):
  - 빈 배고픔: (16, 27) ~ (25, 36)
  - 반 배고픔: (61, 27) ~ (70, 36)
  - 전체 배고픔: (52, 27) ~ (61, 36)
```

### 2. 플러그인 수정 (ActionBar 사용)

기본 체력/배고픔 UI를 숨겼으므로, ActionBar나 Title을 사용해야 합니다.

---

## 🔧 구현 방법

### 방법 1: 기본 UI 숨기기 (간단)

**icons.png 투명화:**
1. Vanilla Minecraft 리소스 추출
2. `assets/minecraft/textures/gui/icons.png` 열기
3. 하트와 배고픔 부분을 투명하게 편집
4. 리소스팩에 추가

**플러그인 코드 변경:**
```java
// ActionBar를 사용하여 커스텀 UI 표시
public void showCustomHUD(Player player) {
    // 체력 바 (왼쪽)
    String healthBar = createHealthBar(player);
    
    // 마나 바 (오른쪽)
    String manaBar = createManaBar(player);
    
    // ActionBar에 표시
    Component hud = Component.text()
        .append(Component.text(healthBar))
        .append(Component.text("     ")) // 간격
        .append(Component.text(manaBar))
        .build();
    
    player.sendActionBar(hud);
}
```

---

### 방법 2: 커스텀 폰트 사용 (고급)

**font/default.json 추가:**

```json
{
  "providers": [
    {
      "type": "bitmap",
      "file": "susurpg:hud/health_bar.png",
      "ascent": -200,
      "height": 9,
      "chars": ["\uE000"]
    },
    {
      "type": "bitmap",
      "file": "susurpg:hud/mana_bar.png",
      "ascent": -200,
      "height": 9,
      "chars": ["\uE001"]
    },
    {
      "type": "space",
      "advances": {
        "\uF800": -1,
        "\uF801": -2,
        "\uF802": -4,
        "\uF803": -8,
        "\uF804": -16,
        "\uF805": -32,
        "\uF806": -64,
        "\uF807": -128
      }
    }
  ]
}
```

---

## 📝 단계별 가이드

### 1단계: 바닐라 리소스 추출

```bash
# 1. Minecraft.jar 찾기
# Windows: %appdata%\.minecraft\versions\1.20.1\1.20.1.jar
# Mac/Linux: ~/.minecraft/versions/1.20.1/1.20.1.jar

# 2. 압축 해제
# assets/minecraft/textures/gui/icons.png 추출
```

### 2단계: icons.png 편집

**필요한 도구:**
- Paint.NET (무료)
- GIMP (무료)
- Photoshop

**편집 방법:**
1. `icons.png` 열기
2. 레이어 > 투명도 설정
3. 하트 영역 선택 (16,0 ~ 25,9 등)
4. Delete 키 (투명하게)
5. 배고픔 영역도 동일하게
6. 저장

### 3단계: 리소스팩 적용

```
1. 편집한 icons.png를 리소스팩에 복사
2. 리소스팩 폴더를 압축 (zip)
3. .minecraft/resourcepacks/ 폴더에 넣기
4. 게임 실행 > 옵션 > 리소스 팩 > 적용
```

---

## 💻 플러그인 코드 수정

### HUDManager.java 수정

```java
package com.susurpg.managers;

import com.susurpg.SusuRPG;
import com.susurpg.data.PlayerData;
import net.kyori.adventure.text.Component;
import net.kyori.adventure.text.format.NamedTextColor;
import org.bukkit.Bukkit;
import org.bukkit.entity.Player;
import org.bukkit.scheduler.BukkitRunnable;

import java.util.HashMap;
import java.util.Map;
import java.util.UUID;

/**
 * ActionBar를 사용한 커스텀 HUD (체력 좌측, 마나 우측)
 */
public class HUDManager {
    
    private final SusuRPG plugin;
    
    public HUDManager(SusuRPG plugin) {
        this.plugin = plugin;
        startUpdateTask();
    }
    
    /**
     * HUD 업데이트 (0.1초마다)
     */
    private void startUpdateTask() {
        new BukkitRunnable() {
            @Override
            public void run() {
                for (Player player : Bukkit.getOnlinePlayers()) {
                    updateHUD(player);
                }
            }
        }.runTaskTimer(plugin, 0L, 2L); // 2틱 = 0.1초
    }
    
    /**
     * 개별 플레이어 HUD 업데이트
     */
    private void updateHUD(Player player) {
        PlayerData data = plugin.getPlayerDataManager().getPlayerData(player);
        
        // 체력 바 (왼쪽)
        Component healthBar = createHealthBar(player);
        
        // 마나 바 (오른쪽)
        Component manaBar = createManaBar(data);
        
        // 스태미나 (중앙 하단)
        Component staminaBar = createStaminaBar(data);
        
        // ActionBar에 표시
        Component hud = Component.text()
            .append(healthBar)
            .append(Component.text("          ")) // 간격
            .append(manaBar)
            .build();
        
        player.sendActionBar(hud);
    }
    
    /**
     * 체력 바 생성 (하트 위치)
     */
    private Component createHealthBar(Player player) {
        double health = player.getHealth();
        double maxHealth = player.getMaxHealth();
        int hearts = (int) Math.ceil(health / 2.0);
        int maxHearts = (int) Math.ceil(maxHealth / 2.0);
        
        // 색상 결정
        NamedTextColor color;
        if (health / maxHealth > 0.5) {
            color = NamedTextColor.RED;
        } else if (health / maxHealth > 0.25) {
            color = NamedTextColor.GOLD;
        } else {
            color = NamedTextColor.DARK_RED;
        }
        
        // 하트 표시
        String filled = "❤".repeat(hearts);
        String empty = "♡".repeat(maxHearts - hearts);
        
        return Component.text()
            .append(Component.text(filled, color))
            .append(Component.text(empty, NamedTextColor.DARK_GRAY))
            .append(Component.text(" "))
            .append(Component.text(String.format("%.1f", health), NamedTextColor.WHITE))
            .build();
    }
    
    /**
     * 마나 바 생성 (배고픔 위치)
     */
    private Component createManaBar(PlayerData data) {
        int mana = data.getCurrentMana();
        int maxMana = data.getMaxMana();
        int bars = (int) Math.ceil(mana / 2.0);
        int maxBars = (int) Math.ceil(maxMana / 2.0);
        
        // 마나 표시
        String filled = "✦".repeat(bars);
        String empty = "✧".repeat(maxBars - bars);
        
        return Component.text()
            .append(Component.text(filled, NamedTextColor.BLUE))
            .append(Component.text(empty, NamedTextColor.DARK_GRAY))
            .append(Component.text(" "))
            .append(Component.text(String.format("%d", mana), NamedTextColor.WHITE))
            .build();
    }
    
    /**
     * 스태미나 바 생성 (인벤토리 위)
     */
    private Component createStaminaBar(PlayerData data) {
        int stamina = data.getCurrentStamina();
        int maxStamina = data.getMaxStamina();
        int bars = (int) Math.ceil(stamina / 2.0);
        int maxBars = (int) Math.ceil(maxStamina / 2.0);
        
        String filled = "⚡".repeat(bars);
        String empty = "⚐".repeat(maxBars - bars);
        
        return Component.text()
            .append(Component.text(filled, NamedTextColor.YELLOW))
            .append(Component.text(empty, NamedTextColor.DARK_GRAY))
            .build();
    }
}
```

---

## 🎯 최종 결과

### 화면 레이아웃

```
┌─────────────────────────────────────┐
│                                     │
│                게임 화면              │
│                                     │
│                                     │
│                                     │
│                                     │
│                                     │
│                                     │
├─────────────────────────────────────┤
│ ❤❤❤❤❤♡♡♡♡♡ 10.0          ✦✦✦✦✦✧✧✧✧✧ 10 │ ← ActionBar
├─────────────────────────────────────┤
│ [1] [2] [3] [4] [5] [6] [7] [8] [9]│
│ [Q] [W] [E] [A] [S] [D] [X] [Y] [Z]│
│ [I] [O] [P]         [,] [.] [/]    │
│            ⚡⚡⚡⚡⚡⚐⚐⚐⚐⚐            │ ← 스태미나
└─────────────────────────────────────┘
```

---

## 📦 리소스팩 다운로드 링크

### 직접 만들기

**1. icons.png 다운로드:**
```
바닐라 리소스에서 추출:
.minecraft/versions/1.20.1/1.20.1.jar
→ assets/minecraft/textures/gui/icons.png
```

**2. 편집:**
- 하트 부분 투명화
- 배고픔 부분 투명화

**3. 리소스팩 폴더 구조:**
```
SusuRPG-ResourcePack.zip
└── pack.mcmeta
└── pack.png (선택)
└── assets/
    └── minecraft/
        └── textures/
            └── gui/
                └── icons.png (편집된 버전)
```

---

## ⚠️ 주의사항

### 1. 리소스팩 필수
- 기본 UI를 숨기려면 리소스팩 필요
- 모든 플레이어가 설치해야 함
- 서버에서 강제 적용 가능 (server.properties)

### 2. 서버 리소스팩 설정
```properties
# server.properties
resource-pack=https://your-server.com/SusuRPG-ResourcePack.zip
resource-pack-sha1=<SHA1 해시>
require-resource-pack=true
```

### 3. 호환성
- Minecraft 1.20.1 기준
- pack_format=15
- 다른 버전은 pack_format 변경 필요

---

## 🔄 대안: BossBar만 사용

리소스팩 없이 BossBar만으로 구현:

```java
// 체력 BossBar (상단)
BossBar healthBar = Bukkit.createBossBar(
    "§c❤ 체력 20.0/20.0",
    BarColor.RED,
    BarStyle.SEGMENTED_20
);

// 마나 BossBar (상단)
BossBar manaBar = Bukkit.createBossBar(
    "§9✦ 마나 15/20",
    BarColor.BLUE,
    BarStyle.SEGMENTED_10
);

// 스태미나 BossBar (상단)
BossBar staminaBar = Bukkit.createBossBar(
    "§e⚡ 스태미나 18/20",
    BarColor.YELLOW,
    BarStyle.SEGMENTED_10
);
```

**장점:**
- 리소스팩 불필요
- 모든 플레이어에게 동일

**단점:**
- 하트/배고픔 UI가 그대로 표시됨
- 위치 조정 불가 (상단 고정)

---

## 🎨 리소스팩 제작 도구

### 추천 프로그램
1. **Paint.NET** (무료)
   - Windows 전용
   - 간단한 편집

2. **GIMP** (무료)
   - 크로스 플랫폼
   - 고급 기능

3. **Aseprite** (유료)
   - 픽셀 아트 특화
   - 애니메이션 지원

### 온라인 도구
- **Pixlr** (무료)
- **Photopea** (무료)

---

어떤 방식으로 구현하시겠습니까?

1. **리소스팩 + ActionBar** (완전 커스텀)
2. **BossBar만 사용** (간단, 현재 v1.5.0)
3. **하이브리드** (리소스팩으로 기본 UI 숨기고 BossBar 사용)
