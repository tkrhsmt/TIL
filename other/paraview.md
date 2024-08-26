# Paraviewの使い方

## pythonスクリプトを使った，視点移動

z軸を上向きにした，斜めの視点を作る．

1. 最初に，`Set view direction to -Y`の視点にする

2. 以下のpythonスクリプトを使って，視点を変更する

    ```python
    camera = GetActiveCamera()
    camera.Azimuth(-45)
    camera.Elevation(45)
    Render()
    ```
