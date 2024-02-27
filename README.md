# 使用 docker manifest 命令来创建和推送 manifest list 测试多平台镜像推送一个tag  
![Watchers](https://img.shields.io/github/watchers/smallflowercat1995/docker-arch-test) ![Stars](https://img.shields.io/github/stars/smallflowercat1995/docker-arch-test) ![Forks](https://img.shields.io/github/forks/smallflowercat1995/docker-arch-test) ![Vistors](https://visitor-badge.laobi.icu/badge?page_id=smallflowercat1995.docker-arch-test) ![LICENSE](https://img.shields.io/badge/license-CC%20BY--SA%204.0-green.svg)
<a href="https://star-history.com/#smallflowercat1995/docker-arch-test&Date">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/svg?repos=smallflowercat1995/docker-arch-test&type=Date&theme=dark" />
    <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/svg?repos=smallflowercat1995/docker-arch-test&type=Date" />
    <img alt="Star History Chart" src="https://api.star-history.com/svg?repos=smallflowercat1995/docker-arch-test&type=Date" />
  </picture>
</a>

## <a href="https://hub.docker.com/r/smallflowercat1995/docker-arch-test" title="docker制作好的测试镜像">docker制作好的测试镜像</a>

## debian12 安装 qemu
    sudo eatmydata aptitude --without-recommends -o APT::Get::Install-Recommends="true" -o APT::Get::Install-Suggests="true" install -y qemu-kvm \
                                                                                                                                qemu-user-static \
                                                                                                                                binfmt-support \
                                                                                                                                qemu-system \
                                                                                                                                qemu-utils
## 通知 docker 使用 qemu 支持多架构编译
    docker run --rm --privileged multiarch/qemu-user-static --reset -p yes

## arch-test.sh 测试材料脚本内容
    #!/bin/ash
    uname -m
    uname -s

## docker-arch-arm64-test 测试材料
    # docker-arch-arm64-test 目录结构
    docker-arch-arm64-test/
    ├── arch-test.sh
    ├── docker-compose.yml
    └── Dockerfile

    # docker-compose.yml 内容
    version: "3.9"
    services:
    arch-app:
        build:
        context: .
        platform: linux/arm64/v8
        image: smallflowercat1995/docker-arch-test:arm64
        command:
        - "/bin/ash"
        - "/app/arch-test.sh"

    # Dockerfile 内容
    FROM --platform=linux/arm64/v8 alpine:latest
    WORKDIR /app
    COPY arch-test.sh /app
    CMD ["/bin/ash", "/app/arch-test.sh"]

## docker-arch-amd64-test 测试材料
    # docker-arch-amd64-test 目录结构
    docker-arch-amd64-test/
    ├── arch-test.sh
    ├── docker-compose.yml
    └── Dockerfile

    # docker-compose.yml 内容
    version: "3.9"
    services:
    arch-app:
        build:
        context: .
        platform: linux/amd64
        image: smallflowercat1995/docker-arch-test:amd64
        command:
        - "/bin/ash"
        - "/app/arch-test.sh"

    # Dockerfile 内容
    FROM --platform=linux/amd64 alpine:latest
    WORKDIR /app
    COPY arch-test.sh /app
    CMD ["/bin/ash", "/app/arch-test.sh"]

## arm64 平台架构编译推送
    cd docker-arch-arm64-test
    docker build -f Dockerfile -t smallflowercat1995/docker-arch-test:arm64 .
    # docker-compose build
    cd -

## amd64 平台架构编译推送
    cd docker-arch-amd64-test
    docker build -f Dockerfile -t smallflowercat1995/docker-arch-test:amd64 .
    # docker-compose build
    cd -

## docker manifest 创建列表 tag 并推送
    docker manifest create smallflowercat1995/docker-arch-test:latest smallflowercat1995/docker-arch-test:arm64 smallflowercat1995/docker-arch-test:amd64
    docker manifest push smallflowercat1995/docker-arch-test:latest

## 清理本地镜像
    docker rmi smallflowercat1995/docker-arch-test:latest smallflowercat1995/docker-arch-test:arm64 smallflowercat1995/docker-arch-test:amd64

## arm64 平台架构打印测试
    docker run --rm --platform linux/arm64 smallflowercat1995/docker-arch-test:latest
    # 打印效果
        aarch64
        Linux

## amd64 平台架构打印测试
    docker run --rm --platform linux/amd64 smallflowercat1995/docker-arch-test:latest
    # 打印效果
        x86_64
        Linux

## 测试成功，此方案可行有效
