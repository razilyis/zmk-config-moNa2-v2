COROPITを使用する方は以下のようにコードを編集してください。

mona2.dtsi

修正前
```
    trackball_central_listener: trackball_central_listener {
        compatible = "zmk,input-listener";
        status = "disabled";
        input-processors = 
            <&zip_xy_transform INPUT_TRANSFORM_Y_INVERT>;       //Y軸反転
            //<&zip_xy_transform INPUT_TRANSFORM_XY_SWAP>,        //X軸とY軸の入れ替え
            //<&zip_xy_transform INPUT_TRANSFORM_X_INVERT>,       //X軸反転
            //<&zip_temp_layer 5 500>;                            //オートマウスレイヤーの選択
    };
```
**修正後**
```
    trackball_central_listener: trackball_central_listener {
        compatible = "zmk,input-listener";
        status = "disabled";
        input-processors = 
            <&zip_xy_transform INPUT_TRANSFORM_X_INVERT>;       //X軸反転
            //<&zip_xy_transform INPUT_TRANSFORM_Y_INVERT>,     //Y軸反転
            //<&zip_xy_transform INPUT_TRANSFORM_XY_SWAP>,      //X軸とY軸の入れ替え
            //<&zip_temp_layer 5 500>;                          //オートマウスレイヤーの選択
    };
```
