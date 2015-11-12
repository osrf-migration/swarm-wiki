A terrain test plugin and python script provide the ability to generate CSV files and PNG images that represent a terrain's elevation and type (plain, forest, buildings).

Thanks to @esquires for providing this tool.

# Usage
Two example worlds are provided that use the terrain test plugin. These are `terrain_test.world` and `terrain_test_roberts.world`. The first world uses the default terrain, and the second uses a DEM of Camp Roberts.

1. Compile and install the swarm code and example code as usual

1. Within the `swarm` build directory, run `gzserver` to generate the `elevation.csv` and `terrain.csv` files.

    ```
gzserver worlds/terrain_test_roberts.world --iters 1
    ```

1. Generate PNG representations of the elevation and terrain type (you may need to install `python-pandas` and `python-ipdb`).

    ```
python ../tools/terrain.py
    ```

1. Open the two PNG files

    ```
eog *.png
    ```