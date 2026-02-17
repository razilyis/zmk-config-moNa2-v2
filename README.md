# compatible_DYAStudio メモ

`moNa2` で [DYA Studio](https://studio.dya.cormoran.works/) 関連機能を使うための導入メモです。

## DYA Studioと参照モジュール（URL）

- [DYA Studio](https://studio.dya.cormoran.works/)
- [runtime-input-processor](https://github.com/cormoran/zmk-module-runtime-input-processor)
- [runtime-sensor-rotate](https://github.com/cormoran/zmk-behavior-runtime-sensor-rotate)
- [battery-history](https://github.com/cormoran/zmk-module-battery-history)
- [ble-management](https://github.com/cormoran/zmk-module-ble-management)
- [settings-rpc](https://github.com/cormoran/zmk-module-settings-rpc)

## 1. `config/west.yml` 追記

```yaml
manifest:
  remotes:
    - name: cormoran
      url-base: https://github.com/cormoran
    - name: badjeff
      url-base: https://github.com/badjeff

  projects:
    # DYA対応フォークのZMK本体
    - name: zmk
      remote: cormoran
      revision: v0.3-branch+dya
      import: app/west.yml

    # DYA関連モジュール
    - name: zmk-module-ble-management
      remote: cormoran
      revision: main

    - name: zmk-module-battery-history
      remote: cormoran
      revision: main

    - name: zmk-module-settings-rpc
      remote: cormoran
      revision: main

    - name: zmk-module-runtime-input-processor
      remote: cormoran
      revision: main

    - name: zmk-behavior-runtime-sensor-rotate
      remote: cormoran
      revision: main

    # PMW3610ドライバ
    - name: zmk-pmw3610-driver
      remote: badjeff
      revision: zmk-0.3

  self:
    path: config
```

## 2. `mona2_r.conf`（Central側）追記

```conf
# ZMK Studio 本体
CONFIG_ZMK_STUDIO=y
CONFIG_ZMK_STUDIO_LOCKING=y

# zmk-module-ble-management
CONFIG_ZMK_BLE_MANAGEMENT=y
CONFIG_ZMK_BLE_MANAGEMENT_STUDIO_RPC=y

# zmk-module-battery-history
CONFIG_ZMK_BATTERY_HISTORY=y
CONFIG_ZMK_BATTERY_HISTORY_STUDIO_RPC=y
CONFIG_ZMK_BATTERY_SKIP_IF_USB_POWERED=n

# zmk-module-settings-rpc
CONFIG_ZMK_SETTINGS_RPC=y
CONFIG_ZMK_SETTINGS_RPC_STUDIO=y

# Split設定同期の土台（RPC系モジュール共通）
CONFIG_ZMK_SPLIT_RELAY_EVENT=y
CONFIG_ZMK_SPLIT_BLE_CENTRAL_SPLIT_RUN_STACK_SIZE=3096

# zmk-module-runtime-input-processor
CONFIG_ZMK_RUNTIME_INPUT_PROCESSOR=y
CONFIG_ZMK_RUNTIME_INPUT_PROCESSOR_STUDIO_RPC=y

# zmk-behavior-runtime-sensor-rotate
CONFIG_ZMK_RUNTIME_SENSOR_ROTATE=y
CONFIG_ZMK_RUNTIME_SENSOR_ROTATE_STUDIO_RPC=y

# 設定永続化（複数モジュール共通）
CONFIG_SETTINGS=y
CONFIG_ZMK_SETTINGS_SAVE_DEBOUNCE=10000
```

## 3. `mona2_l.conf`（Peripheral側）追記

```conf
# zmk-module-battery-history
CONFIG_ZMK_BATTERY_HISTORY=y

# zmk-module-settings-rpc
CONFIG_ZMK_SETTINGS_RPC=y

# Split設定同期の土台（RPC系モジュール共通）
CONFIG_ZMK_SPLIT_RELAY_EVENT=y

# 設定永続化（複数モジュール共通）
CONFIG_SETTINGS=y
CONFIG_ZMK_SETTINGS_SAVE_DEBOUNCE=10000
```

※ `CONFIG_ZMK_RUNTIME_SENSOR_ROTATE=y` は Central 側（`mona2_r.conf`）のみで有効化する。  
Peripheral 側（`mona2_l.conf`）で有効化すると、ビルド時に `zmk_behavior_queue_add` のリンクエラーが出る場合がある。

## 4. CI workflow の整合

`.github/workflows/build.yml` の reusable workflow も DYA 対応フォークへ合わせる。

```yaml
jobs:
  build:
    uses: cormoran/zmk/.github/workflows/build-user-config.yml@v0.3-branch+dya
```

## 5. `.keymap` 追記（`#include`）

`.keymap` に追加する include を、必須/任意で整理。

### 5-1. 必須（機能を使う場合）

```dts
#include <input/processors.dtsi>                       // zip_xy_* など input processor を使う場合
#include <input/processors/runtime-input-processor.dtsi> // runtime-input-processor を使う場合
#include <behaviors/runtime-sensor-rotate.dtsi>        // runtime-sensor-rotate を使う場合
```

### 5-2. 任意（使う場合のみ）

```dts
#include <behaviors/battery_history_request.dtsi> // battery history request behavior を keymap に置く場合
```

## 6. `.keymap` 追記（runtime-sensor-rotate）

`zmk-behavior-runtime-sensor-rotate` は `west.yml` と `.conf` だけでは動かず、`.keymap` にも追記が必要。

### 6-1. behavior を定義

```dts
behaviors {
    rsr_vol: rsr_vol {
        compatible = "zmk,behavior-runtime-sensor-rotate";
        #sensor-binding-cells = <0>;
        tap-ms = <20>;
        cw-binding = <&kp C_VOL_DN>;
        ccw-binding = <&kp C_VOL_UP>;
    };

    rsr_pg: rsr_pg {
        compatible = "zmk,behavior-runtime-sensor-rotate";
        #sensor-binding-cells = <0>;
        tap-ms = <20>;
        cw-binding = <&kp PG_UP>;
        ccw-binding = <&kp PAGE_DOWN>;
    };
};
```

### 6-2. 各レイヤーの `sensor-bindings` を差し替え

例:

```dts
sensor-bindings = <&rsr_vol>;
sensor-bindings = <&rsr_pg>;
```

## 7. `mona2_r.overlay`（runtime-input-processor の listener 設定）

`CONFIG_ZMK_RUNTIME_INPUT_PROCESSOR=y` を有効にするだけでは不十分で、`input-listener` 側にも runtime processor の指定が必要です。

```dts
#include <input/processors.dtsi>
#include <input/processors/runtime-input-processor.dtsi>

&trackball_central_listener {
    status = "okay";
    device = <&trackball_central>;
    input-processors = <&mouse_runtime_input_processor>;

    scroller {
        layers = <3>;
        input-processors = <&zip_xy_to_scroll_mapper &scroll_runtime_input_processor>;
    };
};
```

補足:
- 既存の固定 `zip_xy_transform` / `zip_scroll_transform` / `zip_scroll_scaler` を残すと、runtime 側調整と責務が重複しやすいです。
- まずは上記の最小構成に寄せてから、必要な変換だけ追加するのが安全です。
