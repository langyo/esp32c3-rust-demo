# 备忘

- 只能在 WSL 下构建，如果直接在 Windows 下构建，esp-idf 会因为文件路径长度超过 32 位的问题强制退出构建，比较傻逼
  - 如果直接在宿主机尝试构建的话，[WSL 下的 esp-idf 启用流程](https://narukara.github.io/rust-on-esp-book-zh-cn/installation/riscv-and-xtensa.html)，装好之后，执行一下 `. ~/export-esp.sh`，然后就可以在 WSL 下使用 esp-idf 了
    - 但是仍然构建失败，似乎还是因为路径太长或者环境污染导致的
    - 强烈建议还是老老实实用容器
- [esp-idf 的隔离镜像使用流程](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32/api-guides/tools/idf-docker-image.html)
  - 但是这个容器默认真的只带一个 esp-idf，没有 rust 工具链
  - [带 rust 工具链的在这里](https://narukara.github.io/rust-on-esp-book-zh-cn/installation/using-containers.html)，`docker run --rm -v .:/project -w /project -e HOME=/tmp espressif/idf-rust:esp32c3_latest cargo build`
  - 开个容器实在太耗时了，所以如果是实时开发，上头这个指令可以改一下，`cargo build` 换成 `bash` 以实时交互
- [Wokwi 模拟器使用流程](https://narukara.github.io/rust-on-esp-book-zh-cn/tooling/simulating/wokwi.html)
  - 在 VSCode 安装 Wokwi 插件其实就能用了，安装插件后点开 diagram.json，Wokwi 会自动接管窗口
  - Wokwi 插件其实是商业插件，但是可以个人免费使用，只是需要到官网拿个 30 天的个人许可证——到期了再续就行
- 以 release 模式编译，成品 elf 仍然可能很大，不过实际烧录到芯片上的 bin 应该会小很多
  - 要查看各个段落的占用，先进容器装个 `cargo-binutils`：
    - `cargo install cargo-binutils`
    - `rustup add component llvm-tools`
  - 编译时所有优化选项全开
  - 在容器内运行 `cargo size --release -- -A`，可以看到各个段落的占用
