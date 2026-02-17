# compatible_DYAStudio 繝｡繝｢

`moNa2` 縺ｧ [DYA Studio](https://studio.dya.cormoran.works/) 髢｢騾｣讖溯・繧剃ｽｿ縺・◆繧√・蟆主・繝｡繝｢縺ｧ縺吶・

## DYA Studio縺ｨ蜿ら・繝｢繧ｸ繝･繝ｼ繝ｫ・・RL・・

- [DYA Studio](https://studio.dya.cormoran.works/)
- [runtime-input-processor](https://github.com/cormoran/zmk-module-runtime-input-processor)
- [runtime-sensor-rotate](https://github.com/cormoran/zmk-behavior-runtime-sensor-rotate)
- [battery-history](https://github.com/cormoran/zmk-module-battery-history)
- [ble-management](https://github.com/cormoran/zmk-module-ble-management)
- [settings-rpc](https://github.com/cormoran/zmk-module-settings-rpc)

## 1. `config/west.yml` 霑ｽ險・

```yaml
manifest:
  remotes:
    - name: cormoran
      url-base: https://github.com/cormoran
    - name: badjeff
      url-base: https://github.com/badjeff

  projects:
    # DYA蟇ｾ蠢懊ヵ繧ｩ繝ｼ繧ｯ縺ｮZMK譛ｬ菴・
    - name: zmk
      remote: cormoran
      revision: v0.3-branch+dya
      import: app/west.yml

    # DYA髢｢騾｣繝｢繧ｸ繝･繝ｼ繝ｫ
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

    # PMW3610繝峨Λ繧､繝・
    - name: zmk-pmw3610-driver
      remote: badjeff
      revision: zmk-0.3

  self:
    path: config
```

## 2. `mona2_r.conf`・・entral蛛ｴ・芽ｿｽ險・

```conf
# ZMK Studio 譛ｬ菴・
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

# Split險ｭ螳壼酔譛溘・蝨溷床・・PC邉ｻ繝｢繧ｸ繝･繝ｼ繝ｫ蜈ｱ騾夲ｼ・
CONFIG_ZMK_SPLIT_RELAY_EVENT=y
CONFIG_ZMK_SPLIT_BLE_CENTRAL_SPLIT_RUN_STACK_SIZE=3096

# zmk-module-runtime-input-processor
CONFIG_ZMK_RUNTIME_INPUT_PROCESSOR=y
CONFIG_ZMK_RUNTIME_INPUT_PROCESSOR_STUDIO_RPC=y

# zmk-behavior-runtime-sensor-rotate
CONFIG_ZMK_RUNTIME_SENSOR_ROTATE=y
CONFIG_ZMK_RUNTIME_SENSOR_ROTATE_STUDIO_RPC=y

# 險ｭ螳壽ｰｸ邯壼喧・郁､・焚繝｢繧ｸ繝･繝ｼ繝ｫ蜈ｱ騾夲ｼ・
CONFIG_SETTINGS=y
CONFIG_ZMK_SETTINGS_SAVE_DEBOUNCE=10000
```

## 3. `mona2_l.conf`・・eripheral蛛ｴ・芽ｿｽ險・

```conf
# zmk-module-battery-history
CONFIG_ZMK_BATTERY_HISTORY=y

# zmk-module-settings-rpc
CONFIG_ZMK_SETTINGS_RPC=y

# Split險ｭ螳壼酔譛溘・蝨溷床・・PC邉ｻ繝｢繧ｸ繝･繝ｼ繝ｫ蜈ｱ騾夲ｼ・
CONFIG_ZMK_SPLIT_RELAY_EVENT=y

# 險ｭ螳壽ｰｸ邯壼喧・郁､・焚繝｢繧ｸ繝･繝ｼ繝ｫ蜈ｱ騾夲ｼ・
CONFIG_SETTINGS=y
CONFIG_ZMK_SETTINGS_SAVE_DEBOUNCE=10000
```

窶ｻ `CONFIG_ZMK_RUNTIME_SENSOR_ROTATE=y` 縺ｯ Central 蛛ｴ・・mona2_r.conf`・峨・縺ｿ縺ｧ譛牙柑蛹悶☆繧九・ 
Peripheral 蛛ｴ・・mona2_l.conf`・峨〒譛牙柑蛹悶☆繧九→縲√ン繝ｫ繝画凾縺ｫ `zmk_behavior_queue_add` 縺ｮ繝ｪ繝ｳ繧ｯ繧ｨ繝ｩ繝ｼ縺悟・繧句ｴ蜷医′縺ゅｋ縲・

## 4. CI workflow 縺ｮ謨ｴ蜷・

`.github/workflows/build.yml` 縺ｮ reusable workflow 繧・DYA 蟇ｾ蠢懊ヵ繧ｩ繝ｼ繧ｯ縺ｸ蜷医ｏ縺帙ｋ縲・

```yaml
jobs:
  build:
    uses: cormoran/zmk/.github/workflows/build-user-config.yml@v0.3-branch+dya
```

## 5. `.keymap` 霑ｽ險假ｼ・#include`・・

`.keymap` 縺ｫ霑ｽ蜉縺吶ｋ include 繧偵∝ｿ・・莉ｻ諢上〒謨ｴ逅・・

### 5-1. 蠢・茨ｼ域ｩ溯・繧剃ｽｿ縺・ｴ蜷茨ｼ・

```dts
#include <input/processors.dtsi>                       // zip_xy_* 縺ｪ縺ｩ input processor 繧剃ｽｿ縺・ｴ蜷・
#include <input/processors/runtime-input-processor.dtsi> // runtime-input-processor 繧剃ｽｿ縺・ｴ蜷・
#include <behaviors/runtime-sensor-rotate.dtsi>        // runtime-sensor-rotate 繧剃ｽｿ縺・ｴ蜷・
```

### 5-2. 莉ｻ諢擾ｼ井ｽｿ縺・ｴ蜷医・縺ｿ・・

```dts
#include <behaviors/battery_history_request.dtsi> // battery history request behavior 繧・keymap 縺ｫ鄂ｮ縺丞ｴ蜷・
```

## 6. `.keymap` 霑ｽ險假ｼ・untime-sensor-rotate・・

`zmk-behavior-runtime-sensor-rotate` 縺ｯ `west.yml` 縺ｨ `.conf` 縺縺代〒縺ｯ蜍輔°縺壹～.keymap` 縺ｫ繧りｿｽ險倥′蠢・ｦ√・

### 6-1. behavior 繧貞ｮ夂ｾｩ

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

### 6-2. 蜷・Ξ繧､繝､繝ｼ縺ｮ `sensor-bindings` 繧貞ｷｮ縺玲崛縺・

萓・

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
        input-processors =
            <&zip_xy_transform INPUT_TRANSFORM_X_INVERT
             &zip_xy_to_scroll_mapper
             &zip_scroll_transform INPUT_TRANSFORM_X_INVERT
             &zip_scroll_scaler 1 5
             &scroll_runtime_input_processor>;
    };
};
```

補足:
- 現在の `mona2_r.overlay` は、既存の `zip_*` 変換を維持したまま `&scroll_runtime_input_processor` を追加する構成です。
- 既存挙動を保ったまま DYA Studio 側の runtime 調整を使いたい場合はこの構成が安全です。
