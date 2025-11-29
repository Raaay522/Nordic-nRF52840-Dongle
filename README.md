# [筆記] Nordic nRF52840 Dongle 環境建置

在 Windows 環境下建立 **Nordic nRF52840 Dongle** 的專業開發環境。
使用業界標準的 **VS Code + nRF Connect SDK (基於 Zephyr RTOS)**。

:::info
**硬體設備：** Nordic nRF52840 Dongle
**作業系統：** Windows 11
**開發工具：** VS Code, nRF Connect for Desktop( Programmer、Toolchain Manager)
:::

## 1. 核心觀念釐清

在開始之前，必須理解這支 Dongle 與一般開發板 (DK) 的不同：
* **沒有內建 J-Link：** 無法像一般開發板那樣在 VS Code 裡按一下「Flash」就自動上傳。
* **Bootloader 機制：** 必須手動進入 DFU 模式 (紅燈呼吸)，透過 USB 燒錄 `.hex` 檔。
* **開發框架：** 本教學使用 Nordic 官方推薦的 NCS (Zephyr RTOS)

---

## 2. 環境安裝步驟

### Step 1: 安裝 Nordic 工具
1. 下載並安裝 **[nRF Connect for Desktop](https://www.nordicsemi.com/Software-and-tools/Development-Tools/nRF-Connect-for-desktop)**。
2. 開啟程式，找到 **Toolchain Manager `(v1.6.1)`**，點擊 Install 並開啟。
3. 在列表中選擇 **nRF Connect SDK `(v2.9.2)`**，進行安裝。
4. 安裝完成後，直接點擊旁邊的 **"Open VS Code"** 。
   > **注意：** 透過此按鈕開啟 VS Code 會自動設定好所有路徑，避免環境變數錯誤。

### Step 2: 安裝 VS Code 外掛
* 在 VS Code 中，安裝 **nRF Connect for VS Code Extension Pack** 。

---

## 3. 建立第一個專案 (Blinky)

1. 在 VS Code 左側點擊 **nRF Connect** 圖示。
2. 選擇 **Create a new application** > **Copy a sample**。
3. 搜尋 `blinky`，選擇 `Zephyr/samples/basic/blinky`。
4. **選擇開發板 (Board)：**
   * 請搜尋並選擇：`nrf52840dongle_nrf52840`
   * :warning: **警告：** 千萬不要選到 `nrf52840dk`，兩者腳位定義不同，選錯燈不會亮。
5. 點擊 **Create Application**。

---

## 4. 編譯與燒錄流程

### 編譯 (Build)
1. 在 **Build Configurations** 區塊，點擊 **Build**。
2. 等待終端機跑完，直到出現 `Build completed`。
3. 產生的燒錄檔位於專案資料夾下的：`build/zephyr/zephyr.hex`。

### 燒錄 (Flash)
1. 開啟 **nRF Connect for Desktop** > **Programmer`(v4.6.2)`**。
2. **進入 DFU 模式：**
   * 將 Dongle 插上電腦。
   * 按下側邊的 **Reset 按鈕**。
   * 確認 **紅色 LED 開始呼吸 (忽明忽暗)**。
3. **載入檔案：** 將剛剛生成的 `zephyr.hex` 拖拉進 Programmer 視窗。
4. **寫入：** 點擊 **Write**。
5. 完成後 Dongle 會自動重啟，綠色 LED 開始閃爍，代表成功！

---

## 5. 常見問題 (FAQ)

### Q: 編譯時出現 `UnicodeDecodeError: 'cp950' codec can't decode...`？
**A:** 在繁體中文 Windows 上，Python 預設使用 Big5 (CP950) 編碼，讀取 SDK 的 UTF-8 設定檔時會導致 `UnicodeDecodeError` 崩潰。

**解決方案：**
1. 進入 Windows **「編輯系統環境變數」**。
2. 在 **「系統變數」** 區域新增：
    * **變數名稱：** `PYTHONUTF8`
    * **變數值：** `1`
3. **完全關閉並重啟 VS Code** 讓設定生效。
