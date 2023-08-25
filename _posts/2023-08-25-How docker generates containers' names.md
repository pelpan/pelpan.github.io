---
layout:     post
title:      Docker 引擎是如何生成容器名称的？
subtitle:   每次你使用docker创建一个容器，如果没有传递`--name`参数，docker 会帮你选择一个容器名, 你有想过这个名称是怎么来的吗?
date:       2023-08-25
author:     Pele
header-img: img/docker.webp
catalog: true
tags:
    - E2C
    - Docker
---

> 这是一篇E2C(English to Chinese)文章，系本人手动翻译的英语文章，所有内容都不是本人原创。
>
> 我做E2C的目的是提升英文文档的阅读水平/拓宽眼界/闲着无聊，如果能够帮助到你就更好了！

本文原文链接🔗 [How docker generates container's names](https://pet2cattle.com/2022/08/docker-container-names-generator) by <u>Jordi Prats</u>

---

每次你使用 docker 创建一个容器，如果没有传递`--name`参数，docker 会帮你选择一个容器名：两个单词中间加一个下划线

```bash
$ docker run --rm -d alpine sleep 24h
38c4cc4e87762fc113ef174e9a4989e13d21037678abd3fe73840b825f14c7bf
$ docker ps
CONTAINER ID   IMAGE                          COMMAND                  CREATED         STATUS                PORTS                    NAMES
38c4cc4e8776   alpine                         "sleep 24h"              5 seconds ago   Up 3 seconds                                   romantic_shtern
```

在这个例子中被选中的是*romantic_shtern*,但是事实上可以使用很多种单词

```bash
$ docker ps --all | grep -v "Up" | awk '{ print $NF }'
NAMES
mystifying_poitras
suspicious_shtern
focused_chatelet
keen_mendel
happy_jackson
xenodochial_margulis
kind_blackburn
gallant_pascal
trusting_thompson
(...)
```

那么，Docker是如何为容器生成名字的呢？

如果查看[moby/moby](https://github.com/moby/moby)仓库(Docker的开发仓库)，可以找到一个名为[`namesgenerator`](https://github.com/moby/moby/blob/master/pkg/namesgenerator/names-generator.go)的包。在这个包中可以看到使用了两个列表`left`和`right`:

- `left`列表里是一些形容词
- `right`列表是一个包含著名的(notable)科学家和黑客名字的列表

包内还包含用于生成容器名的函数，由于需要具有单一性(unique)，当发现冲突时会在后面附加一个随机数字。

```go
 if retry > 0 {
    name += strconv.Itoa(rand.Intn(10)) //nolint:gosec // G404: Use of weak random number generator (math/rand instead of crypto/rand)
  }
```

更有趣的一点是，有一个例外，：当生成的容器名称是`boring_wozniak`时，会重新生成一个，因为[`Steve Wozniak`](https://en.wikipedia.org/wiki/Steve_Wozniak)并不无聊*LOL*

```go
func GetRandomName(retry int) string {
begin:
  name := left[rand.Intn(len(left))] + "_" + right[rand.Intn(len(right))] //nolint:gosec // G404: Use of weak random number generator (math/rand instead of crypto/rand)
  if name == "boring_wozniak" /* Steve Wozniak is not boring */ {
    goto begin
  }

  if retry > 0 {
    name += strconv.Itoa(rand.Intn(10)) //nolint:gosec // G404: Use of weak random number generator (math/rand instead of crypto/rand)
  }
  return name
}
```

---

Posted By  <u>Jordi Prats</u> on 01/08/2022 
如果你觉得这篇文章有趣，欢迎访问原作者的主页查看[原文](https://pet2cattle.com/2022/08/docker-container-names-generator)

