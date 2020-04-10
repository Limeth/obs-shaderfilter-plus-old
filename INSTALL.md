1. Build OBS according to [this guide](https://github.com/obsproject/obs-studio/wiki/Install-Instructions#debian-based-build-directions).
2. Compile this plugin with OBSSourcePath environment variable set to the `libobs` subdirectory of the OBS repository:
    1. `mkdir build; cd build`
    2. `env OBSSourcePath='/home/limeth/workspace/c/obs-studio/libobs' cmake ..; and make -j24`
3. Install by copying built plugin files to OBS plugin directory:
    * Either by running `sudo make install`;
    * or by running `sudo cp libobs-shaderfilter.so /usr/lib/obs-plugins/`
