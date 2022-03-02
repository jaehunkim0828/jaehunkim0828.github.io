---
layout: single
title: "[Docker]맥북 M1 빌드 오류(linux/arm64, linux/amd64)"
toc: true
toc_sticky: true
toc_label: "On this page"
categories: [Docker]
tag: [Docker]
---

## <span style="color: #F3C892">1. 맥북 M1 build</span>

문제점
![docker](/images/dockers/error.png)

> M1 Mac book에서 Dockerfile를 Image로 빌드하고 aws ec2 ubuntu 서버에서 docker pull하면서 오류가 발생했다. 이유는 m1칩에서는 빌드할때 linux/arm64로 빌드가 되어서 ubuntu에서는 run이 되지 않는 것이였습니다.
>
> - 즉 M1은 architecture이 arm 기반이라서 ubuntu에서 열리지 않는것 입니다.
> - M1에서 빌드할때 amd 기반을 빌드할 수 있도록 docker buildx를 사용합니다.

## <span style="color: #F3C892">2. docker buildx 세팅</span>

처음부터 docker buildx를 사용할 수 없기때문에 따로 설정을 해줘야 합니다.

- vim editor으로 설정하기

```
$ vim ~/.docker/config.json
```

![docker](/images/dockers/docker-config.png)

- visual studio에서 설정하기

```
$ code ~/.docker/config.json
```

![docker](/images/dockers/vscode-config.png)

> "experimental": "enabled" 추가해주기

![docker](/images/dockers/docker-buildx.png)

- docker buildx가 잘 실행됩니다.

## <span style="color: #F3C892">2. amd64로 빌드하기</span>

1. Builder Instance

   ```
   $ docker buildx ls
   ```

   ![docker](/images/dockers/docker-ls.png)

   - docker buildx builder를 확인해 볼 수 있습니다. 기본적으로 처음에 default값이 존재하는 것을 확인 할수 있습니다.

2. Builder 생성 & 프로젝트 bulid 하기

   ![docker](/images/dockers/inspect.png)

   - <span style='background-color: #EEEEEE; color: tomato; padding: 0 2px; border-radius: 3px' >--name</span>은 builder 이름
   - <span style='background-color: #EEEEEE; color: tomato; padding: 0 2px; border-radius: 3px' >--driver</span>은 dirver이름(띄어쓰기로 중복가능)
   - <span style='background-color: #EEEEEE; color: tomato; padding: 0 2px; border-radius: 3px' >--pratform</span>은 build할때 지정한 platform 고정할 수 있습니다.
   - <span style='background-color: #EEEEEE; color: tomato; padding: 0 2px; border-radius: 3px' >--use</span>는 현재 builder를 기본사용으로 지정합니다.
   - <span style='background-color: #EEEEEE; color: tomato; padding: 0 2px; border-radius: 3px' >--load</span>는 host docker image에 저장합니다.

   Builder 만들기

   ```
   $ docker buildx --name [name] --dirver [option] --use <-- 예시
   $ docker buildx --name test --dirver linux/amd64 linux/arm64 linux/ppc64le linux/arm/v8 linux/arm/v6
   ```

   buildx로 multi-platform image 만들기

   ```
   $ docker buildx build --platform linux/adm64 --load -t test-example:version
   ```

   aws ubuntu서버에 가서 docker pull해주고 run해주니 잘돌아간다.
   ![build-start](/images/dockers/build-start.png)
