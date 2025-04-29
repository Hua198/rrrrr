name: Build iStoreOS OpenAppFilter
on:
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3

    - name: 安装依赖
      run: |
        sudo apt update
        sudo apt install -y build-essential git subversion libncurses5-dev libssl-dev gawk flex bison python3-distutils

    - name: 克隆 iStoreOS 源码
      run: |
        git clone -b istoreos-22.03 https://github.com/istoreos/istoreos.git istoreos
        cd istoreos
        # 添加 OpenAppFilter 源
        echo 'src-git openappfilter https://github.com/destan19/OpenAppFilter.git;master' >> feeds.conf.default

    - name: 更新并安装 feeds
      run: |
        cd istoreos
        ./scripts/feeds update -a
        ./scripts/feeds install -a

    - name: 配置选项
      run: |
        cd istoreos
        # 示例：直接生成 .config（也可以使用 make menuconfig 编辑）
        cat > .config << 'EOF'
        CONFIG_TARGET_rockchip=y
        CONFIG_TARGET_rockchip rk356x
        CONFIG_PACKAGE_luci-app-oaf=y
        CONFIG_PACKAGE_kmod-oaf=y
        CONFIG_PACKAGE_kmod-oaf-nodist=y
        EOF
        make defconfig

    - name: 开始编译
      run: |
        cd istoreos
        make -j2 V=s

    - name: 上传编译产物
      uses: actions/upload-artifact@v3
      with:
        name: oaf-packages
        path: istoreos/bin/packages/rockchip_armv8/*.ipk
