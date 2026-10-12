---
layout: post
title: 让记录成为习惯、让知识成为财富
permalink: /note/
---

* content
{:toc}

-----------



2024.8.21
-----------
+ [基于go的干净架构](https://github.com/amitshekhariitbhu/go-backend-clean-architecture?tab=readme-ov-file)
  - [http polling,http long polling,websocket,sse的区别](https://www.youtube.com/watch?v=8ksWRX4xV-s)
  - 架构分三层，根据 domain,model,entity分成controller,usercase,reponsitory




2024.3.27
-----------
+ [go的slice内存分本](https://sazak.io/articles/visual-guide-to-slices-in-go-2024-03-25)
  - 当未指定容量时，默认长度与容量相等
  - append会长度扩容，判断是否在容量范围内，如果不在，容量在1024内双倍扩容，超过1024后增加1/4扩容(1.25倍)
  - s1 := int[]{1,2,3,4,5} // 长度 5, 容量 5
  - s2 := s1[1:2] // 长度 1 容量 4，s1的容量减去起始位置的长度
  - s2 = append(s2,99) // s2 = []int{2,99} ，此时s1也会发生变化，s1 = []int{1,2,99,45}
  - s3 = s1[1:2:3] // s不是步长，而是容量到3 ,此时s1的容量减去起始位置的长度 - 3之后的长度,长度1，容量5-1-1=3

2024.3.20
-----------
+ [使用go写前端，wails，类似js的桌面应用electron](https://github.com/wailsapp/wails)
  - QT的桌面应用

2024.3.19
-----------
+ [go map slice新特性](https://henry.precheur.org/go/generics_improvements_for_maps_and_slices/)
  - 1.20开始，引入了golang.org/x/exp/maps和golang.org/x/exp/slices，可以使用maps.Clone maps.Equal和slices.Min slices.Max
  - 其实之前用reflect.DeepEqual 和 reflect.Copy也可以满足需求

+ [goroutine的调度策略](https://blog.devtrovert.com/p/goroutine-scheduler-revealed-youll)
  - GMP： G:轻量级线程goroutine，P:逻辑处理器，M:操作系统线程资源，一个go程序可以使用10000个线程M，而不是10000个Goroutine
  - Goroutine三个状态：运行中 等待运行 可运行 runnable running wating
  - 每个线程P有一个本地队列，长度256
  - 协程创建后，优先找本地队列，若本地队列满则放入全局队列
  - P的大小为机器逻辑处理器的数量
  - M的大小为操作系统线程的数量，一般10000个
  - 一个P需要绑定一个M才能运行P中的goroutine,如果这个P队列中的G全部运行完了，则会从全局队列中获取G，如果全局队列中也没有G，则会从其他P队列中偷G

2024.3.14
-----------
+ [40岁的编程经验](https://liw.fi/40/#index2h1)

2023.12.29
-----------
+ [深度学习中文资料-基于Python](https://zh.d2l.ai/chapter_preface/index.html)


2023.11.25
---------------
+ [小学生看懂的ebpf教程](https://ebpf.io/books/buzzing-across-space-illustrated-childrens-guide-to-ebpf.pdf)


2023.11.15
-------------------
+ [chatPDF，上传一篇pdf，然后基于它去提问，快速找到信息](https://www.chatpdf.com/)


2023.10.19
-----------
+ [架构与设计，来自过早客，长篇好文](https://www.guozaoke.com/t/102976#reply7)


2023.7.14
-----------
+ [rts一个二进制文件，可以自动把请求的 response 转成 go 的 struct](https://github.com/galeone/rts)

+ [强大的gitpod](https://medium.com/@aafenguk/%E8%AF%95%E7%94%A8gitpod%E5%9C%A8%E7%BA%BFide-8e8c751ed64f)
  - 在线的vscode ide，不需要 clone 到本地看代码。
  - 提供外网的 terminal，可以使用 docker 命令。
  * 每个月有 50 个小时的免费使用限制。
  * 对于任何一个Github项目，只需要在前面添加：`[https://gitpod.io#](https://gitpod.io/#)`  就可以使用 gitpod

2023.1.29
-----------
+ [数字游民的网站](https://nomadlist.com/buenos-aires)
  1. 网站是收费的，在解释为什么要收费时，说了这样的一个观点：开源的软件与网络服务不一样。
  以下是翻译：
  ```
   I love free software and could not have built my site without it. But free web services are not like free software. If your free software project suddenly gets popular, you gain resources: testers, developers and people willing to pitch in. If your free website takes off, you lose resources. Your time is spent firefighting and your money all goes to the nice people at Linode.
    我喜欢免费软件,没有它我就无法构建我的网站。 但免费的网络服务不像免费软件。如果您的免费软件项目突然变得流行,您会获得资源:测试人员、开发人员以及有意投入时间的人。如果您的免费网站脱颖而出,您会失去资源。您的时间花在灭火上,您的钱都被付给Linode那些友好的人。
    大概意思是:
    作者喜欢免费开源软件,他的网站就是建立在免费开源软件之上。
    但是免费的网络服务(如SaaS服务)不一样,如果突然变得很火烫,并不会像免费开源软件那样给予更多资源(开发者、贡献者等)。
    相反,免费网站一旦流行起来,作者就会花费更多时间处理问题、应对压力,而花费(如带宽、存储)全都花在提供这个服务上,不会有回报。
    作者用的例子是虚拟主机Linode(他的网站是部署在Linode上的),他的钱全花在Linode上面。
    所以,他的意思是:
    免费开源软件的流行会给予更多资源,而免费网络服务的流行则会消耗更多资源(时间和金钱)。
    两个模式本质上是不一样的。
    总的来说,他是在赞扬免费开源软件的模式,他的网站也是建立在这种模式之上。但是对于免费网络服务,他的看法比较悲观。
    ```
  1. [一个成功的独立开发者相关介绍](https://www.v2ex.com/member/DanielNg23)  
    1. nomad 数字游民
    2. 到了一定阶段后，需要脱离代码，代码更多是在消耗体力
    3. 要有商业意识


2023.1.29
-----------
+ [envoy 社区在支持 go 插件](https://github.com/envoyproxy/envoy/pull/22573)
  1. [写的设计文档非常的专业](https://docs.google.com/document/d/1noApyS0IfmOGmEOHdWk2-BOp0V37zgXMM4MdByr1lQk/edit#heading=h.ks9gl1pw72iu)
  2. 对比了 go 与 lua 、wasm、c++的优劣后，说明了为什么要支持 go 作为插件


2023.1.29
-----------
+ [技术管理的思考，以及 35 岁焦虑](https://www.infoq.cn/article/1y50p46mqc05rxlWsAKl)
  1. 是否适合技术管理？技术管理问三个问题，至少有两个回答“是”才符合。
    - 是否能面临不确定性？核心员工离职
    - 是否能足够强大的心态来面临负面情绪
    - 是不是有比较强烈公开演讲的冲动
  2. 10x 程序员可以完成 10 个程序员的工作量
  3. 技术管理常见的两类：任务分包/进度整合，工作繁重，可替代性强，附加价值低；细节掌控管理者：大部分时间花费在审阅下属的工作中，每件事百分百参与与掌控：容易被管理工作压垮，离职率高：没有人愿意一直被当作新手或是一个保姆型上级。

+ [开源的一些好玩的背后故事](https://www.infoq.cn/article/iwMWAW2Szdu2HFwuebC7)
  1. instagram 是因为女友抱怨照片没有朋友拍得好看
  2. elasticsearch 是因为想帮老婆做一个烹饪的软件，可以搜索食谱

2022.11.10
------------
+ [一个使用 rust 将网页打包成 mac 应用的项目](https://github.com/tw93/Pake)
+ [ 字节工程师关于 web 的思考](https://zhuanlan.zhihu.com/p/581977751)

2022.11.9
----------
+ [k8s 的 json 的 Patch / merge/stategic 区别](https://www.jianshu.com/p/8eb47409bc6d)
  1. [RFC6902 json patch](https://www.rfc-editor.org/rfc/rfc6902), 使用  action path value 的方式操作 json，如 patch   add /api/zackzhangkai/name zack
  2. [RFC7396 json merge patch](https://www.rfc-editor.org/rfc/rfc7396)，k8s 默认为这种 patch 方式。当 patch 列表时，会在列表后面 append 新的元素。
  3. stategic merge patch 是 patch 的变种，在 k8s type 字段中字义是否为这种类型。如 merge在操作列表时，会append。但如果执行为 strategic，则是直接替换replace。

2022.11.4
+ [jq 的常用使用方法](https://stedolan.github.io/jq/tutorial/)
  1. [jq的官方 wiki有更多详细的文档](https://github.com/stedolan/jq/wiki/Cookbook#filter-objects-based-on-the-contents-of-a-key)
  2. [stackoverflow 上的问答](https://stackoverflow.com/questions/26701538/how-to-filter-an-array-of-objects-based-on-values-in-an-inner-array-with-jq)
  1. ` ~ sudo cat /tmp/bb.txt | jq '.dataObject.dataList[0].dataList'  | jq  -c '.[] | select(.[5] | contains("21:","20:","19:"))' | sort  |uniq`
2022.10.31
-----------
+ [beatifyjson 格式化 - 工具](https://www.beautifyjson.org/)
+ [云原生对开发者意味着什么](https://segmentfault.com/a/1190000042716087)
  1. 上云1.0 只是讲应用搬到云的虚拟机上，但缺少对开发过程的关注，如开发框架选型、应用快速部署交付、资源算力弹性管理。这个层面，对开发者而言，只是通过基础设置提升了基础资源的使用率。
  2. 上云 2.0，不再只是讲应用搬到云上虚拟机。核心是：将应用的每个部分进行打包、动态编排，每个部分都能被主动调度和管理。
  3. 通过将基础设施与业务平台融合，这些能力得以“排列组合”到一起，为业务应用提供标准的运行、监控、治理平台。这让开发者将关注点从资源，转移到开发本身。
  4. 比如：微服务通过把应用程序构造成一组服务进行开发部署，大提高了业务的敏捷性；通过 Devops，进行持续集成交付以实现完整的自动化和上云协同。
  5. 通过云原生技术来构建应用，开发者不再需要耗费大量时间去考虑底层技术实现，通过好用的云原生工具，就能快速调用和管理底层资源，所有算力能自动启动、伸缩，快速响应业务，有效提升了资源利用率，降低开发成本。
  6. 得益于此，开发者有更多精力来思考业务。
+ [为什么需要 https](https://www.runoob.com/w3cnote/https-ssl-intro.html)
  1. 证书认证过程：
    - 浏览器访问https 网站
    - 网站返回一个证书，该证书带有公钥、过期时间、cn（网址）
    - 浏览器校验是否过期，网址是否对得上。通过后，生成盐字符串，通过公钥加密给到网站
    - 网站通过私钥解密出盐
    - 之后网站与浏览器通信就通过这个随机盐加密内容
  2. TLS 终止，浏览器 => 负载均衡 （tls 终止）=>  服务 ，优点：
    1. 服务之间通信无 tls速度快
    2. 负载均衡做 tls 卸载后可以识别流量，防止恶意攻击
    3. 减少运维配置的复杂性，无需给每个服务配置 tls 证书
  3. [通过脚本生成自签名证书](https://github.com/envoyproxy/envoy/blob/20e4dd2c0e5bacf07cd2fa7f559ae4714da8f905/examples/double-proxy/verify.sh#L37)

2022.10.28
------------
+ [cors csrf xss 的区别](https://zhuanlan.zhihu.com/p/92255672)
  1. cors 跨域请求资源共享，ajax 回调函数可以请求其他域名资源
  2. csrf 跨域请求伪造，当浏览器登陆某一网站后，本地会生成 cookie，下次再次访问这个网站时，不用登陆。如果在这时访问一个恶意网站，浏览器带上 cookie 后，恶意网站获取 cookie 后，给目的网站发送消息。
  3. xss 恶意网站在网站上放置恶意脚本，当登陆时会泄漏 cookie 用户名 密码。
2022.10.25
------------
+ [ 公众号工具](https://knb.im/mp/)

2022.10.23
----------------
+ [我看绩效考核](https://coolshell.cn/articles/17972.html)
  1. KPI毁了索尼
  2. 做正确的事，然后等着被开。与其去适应糟糕的环境，不如自己去寻找适合的土壤。
  3. [继做正确的事等着被开之后问答](https://mp.weixin.qq.com/s?__biz=MzUyOTA1NTkzNw==&mid=2247484417&idx=1&sn=316f9f6d6ac7cdca97123815a67a665a&chksm=fa67adafcd1024b948caed0e5528c4817a7ef2b1b1a3ab8da34e0ff4231b28c2659ee9951112#rd)

+ [每个程序员都应该看的书籍](https://stackoverflow.com/questions/1711/what-is-the-single-most-influential-book-every-programmer-should-read)

+ [optimizing software in C++](https://www.agner.org/optimize/optimizing_cpp.pdf)

+ [linkdin上必读的code review](https://thenewstack.io/linkedin-code-review/) 

2022.10.18
----------------
+ [ 开源的未来：为什么开源核心已死？](https://www.infoq.cn/article/eQRGLjAzl3mjhhwPTE14)
  1. 10年前，一个项目需要数年时间才能崭露头角。这使得企业有时间来培育自己的项目，比如提供企业商业版。但是现在事务发展快多了，企业如果这样做，使得企业得面临如自己开源的产品竞争。
，比如提供企业商业版。但是现在事务发展快多了，企业如果这样做，使得企业得面临如自己开源的产品竞争。

2022.10.17
----------
+ [ 被裁后如何「心里重建」](https://www.tmtpost.com/6283455.html)
  1. 在寒气逼人的经济形式下，这是一门必修课。
  2. 文中有大量心理学内容。无效安慰（表层安慰、说教式安慰）、框架效应
  3. 解决方式：
    1. 情绪觉察重评（先解决情绪再解决问题）
    2. 能力聚焦重评，明确自己的优势，能力与期望偏差不要太大
    3. 内在激励重评，可以延长找工作的时间等。

2022.10.12
----------
+ [问题到此为止的心态](https://www.mingyantong.com/ju/1706399)

2022.10.11
--------------
+ [工具：Jsoncrack自动格式化成拓扑](https://jsoncrack.com/editor)
  - 用来格式化envoy的配置文件非常好，可以很直观看出xds的关系

2022.9.29
-------------
+ [REST 如何站到了自己的对立面？](https://www.infoq.cn/article/jfkZ7LHF1HbONN2sPOyw)
  + rest 的全名是表征状态转移 represent state transfer。后端返回的 api 中会有 referenceURI，然后页面能自动根据它跳到下一个状态(如通过 html 中的 href)。而不需要前端每次根据返回的状态去调取不同的 api。
  + GraphQL 打破了 rest 的限制，让前端任意去组装自己的查询语句，列式查询，而不需要每次通过后端返回固定的 api 格式再去显示对应的内容。
  + rest 从诞生起就没有被用对过，现在人们对 rest 的理解是：REST=JSON API 称之为伪 Rest

2022.9.23
--------------
+ [微信聊天机器人，可与公众号联动](https://github.com/littlecodersh/ItChat)
+ [用例图 UML 讲解](https://www.cnblogs.com/green-jcx/p/16722916.html)
  + 边界
  + 参与者
  + 用例
  + 关联
  + 扩展

+ [我是如何失去团队掌控的](https://www.v2ex.com/t/882400#reply36)
  + 管理需要专人承担，不要陷于具体事情
  + 人的精力是有限的
  + 管理就就是把正确的人放在正确的位置上

2022.9.21
--------------

+ [回车为什么叫回车？](https://zhuanlan.zhihu.com/p/69176819#:~:text=%E8%87%B3%E4%BA%8E%E8%BF%99%E4%B8%AA%22%E8%BD%A6%22%E6%98%AF%E6%80%8E%E4%B9%88,Return%22%EF%BC%8C%E7%AE%80%E5%86%99%E6%88%90CR%E3%80%82)
  - 最早期的编码是打字机来打孔来实现 0/1 的输入，打字机英文是 Teletypewrite，翻译过来是电传打字机。unix 中经常用的 tty即来源于此。
  - 那如果给你一个打字机，你需要做什么操作？
  - 最常用的操作应该是：1.把打孔的针头回到纸张的最开头；2.打完一行纸张后，需要移动纸张，开始在下一行来打孔。
  - 这就是回车跟换行的来源：1.换行：就是要把纸移动下，LineFeed，feed 是喂/供给的意思，就是供给一行，即换行，ascii 码中\n 即是换行，简写是 LF；
  - 打孔的针头回来最开始 cr carriage Return ,carriage 就是车的意思，意为把车回到最开始，ascii码即是 \r，简写就是 CR。

+ [问题：**既然 ascii 码是 128 位，那其实用 7 位存储就够了，为什么要用 8 位来表示一个byte 呢？**](https://www.zhihu.com/question/367812743)
  + 确实浪费了一位，但是早期，打字机经常出错，因此预留了一位做校验。

2022.9.20
----------
+ [v2ray使用了路由功能自动分流，比pac强大](https://github.com/Loyalsoldier/v2ray-rules-dat)
+ [v2ray的文档](https://github.com/zackzhangkai/zack6116.github.io.git)
+ [gfw list配置proxyOmega时的规则列表](https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt)
+ [google 开源的 基于 k8s 的 CICD 工具](https://skaffold.dev/docs/)
  + istio samples 有示例
  + 主要为了加快开发的docker 打包与部署
  + 原理：源码保存后 ->  触发 dockerfile 自动打包后部署到 k8s

+ go的配置
  ```bash
	GOPRIVATE="code.xxx.cn"
	GONOPROXY="code.xxx.cn"
	GONOSUMDB="code.xxx.cn"
	go env -w GOINSECURE=code.xxx.cn
	git config --global url.git@code.xxx.cn:.insteadOf https://code.xxx.cn/
	go env -w GOPROXY=https://goproxy.cn,direct
  ```

+ ohmyzsh
  + 需要加入以下修复每次只提醒一个字母的问题
    ```
      echo ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE=\'fg=60\' >> $ZSH_CUSTOM/zsh-autosuggestions_custom.zsh
    ```
  + 插件：
    ```
	plugins=(git
      git-open # git clone https://github.com/paulirish/git-open.git $ZSH_CUSTOM/plugins/git-open
	tmux
	extract
	gitignore
	cp
	git-open
	z
	command-not-found
	safe-paste
       	colored-man-pages
	sudo
	history-substring-search
	kubectl
	zsh-syntax-highlighting
	zsh-autosuggestions)
    ```
---------

2022.9.18
---------
+ [spaceVim 通过vim打造一个语言开发IDE](https://spacevim.org/cn/documentation/#%E5%9C%A8%E5%B7%A5%E7%A8%8B%E4%B8%AD%E8%BF%9B%E8%A1%8C%E6%90%9C%E7%B4%A2)
  + 安装
    ```
      curl -sLf https://spacevim.org/cn/install.sh | bash
    ```
  + 配置好vim确实很有必要，如vim的自动补全提示、高亮等，但是没有必要通过vim来开发。做得再好也超不过vscode，直接上手vscode完事。
  + vim的操作要求非常高，其实通过 tmux可以补充vim的很多不好用的地方。
  + 因此最佳实践是 tmux + spaceVim + vscode。
  + 两个比较好的vim的配置：
    + [Vim的安装与配置](https://www.chenshaowen.com/blog/vim-installation-and-configuration-on-windows-os.html)
    + [vim配置入门-阮一峰](http://www.ruanyifeng.com/blog/2018/09/vimrc.html?ivk_sa=1024320u)

2022.9.9
---------
+ [es 使用 mmap 来存储索引文件加速查询](https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-store.html#mmapfs)
  -  通过初始化容器初始化内核参数  
```bash
initContainers:
  - command:
    - sysctl
    - -w
    - vm.max_map_count=262144
```

+ **应用在使用数据库时，通过通过环境变量传入用户名密码，但是这样做很不安全，可以通过 k8s 的 secret 来解决**  
```bash
- name: ELASTIC_USERNAME
    valueFrom:
      secretKeyRef:
        key: username
        name: elastic-credentials
- name: ELASTIC_PASSWORD
  valueFrom:
    secretKeyRef:
      key: password
      name: elastic-credentials
```

+ [helm 详解](https://www.cnblogs.com/liugp/p/16659802.html)
  - `helm lint`测试下 helm 包是否正常；
  - `helm template`把 chart 的包渲染成 yaml；
  - `helm get values `可以获取 release 中的 values 的值  
    ```bash
    helm -n istio-system template ./files/chart/4-csmcontrollermanager-0.1.0 --set image.repo=${REPO} --kubeconfig ${KUBECONFIG} | kubectl  -n istio-system delete -f -
    ```

+ [go 的框架]
  + gin gorm beego go-micro go-kit
  * [ent](https://github.com/ent/ent)

+ [graphQL]
  + 前端自由组合查询，无需后端封装数据库CRUD API

+ [在线编辑、画图、流程图](https://whimsical.com/)

2022.7.22
----------
+ [openshift 安装全过程](https://cloud.tencent.com/developer/article/1640415)
  - 使用 envoy 当作负载均衡
  - 使用 vsphere 作虚机管理
  - 使用 podman 来管理容器

+ [cncf landscape 1119 个开源项目](https://landscape.cncf.io/)

+ [envoy 中文指南](https://icloudnative.io/envoy-handbook/docs/gettingstarted/architecture/)
  - envoy 的架构 listener filter/ filter chain

+ [浅谈 servicemesh 中的 envoy](https://juejin.cn/post/6844903637710274573)
  - vpp 是 dpdk 的实现框架，由思科贡献
  - envoy 在实现 Quic filter来提高效率
  - istio 在结合 ebpf 来提高包的转发效率，解决 iptables 性能问题
  - ebpf 是结合 xdp 来实现的

+ [灵雀云kube-ovn 的介绍](https://mp.weixin.qq.com/s?__biz=MzUxODU1NzY1NQ==&mid=2247484708&idx=1&sn=cac14fdd6b0fe2a40f5676a6f07b2352&source=41#wechat_redirect)
  - ovn 是 ovs 的控制器，实现了路由器的功能，ovs 是 open vswitch 虚拟交换机。ovn open virtual network。
  - 灵雀云的 kube-ovn 是以 k8s 的 namespace 做为一个独立的子网的。flannel 是以node 节点做为一个子网的。

2022.7.12
-----------
+ [go 项目的标准](https://github.com/golang-standards/project-layout/blob/master/README_zh.md)


2022.2.24
---------
+ [frp](https://github.com/fatedier/frp)真是个好东西，可以将本地的服务映射到服务器上，这样服务在访问服务器上的某个服务就直接到了本地。这有三个应用场景：1.可以很快的实现远程连接，如将本地的端口暴露到远端 2.可以把家里闲置的电脑当作一个服务器，如：只需要在公网上有一个服务器，把本地的一服务映射过去。3.方便本地调试，代码在本地跑起来，直接映射到服务器上，把服务器上原先的服务停掉，流量就到了本地。

+ `关于各语言的IDE推荐`，原则：尽量使用jetbrains的IDE、少花钱
  1. Java推荐IDEA
  2. Go 推荐 Goland
  3. C/C++ 推荐使用 Vscode，原因clion破解太难。看仓库，如果仓库中有.vscode的目录，无脑使用vscode就OK。如envoy官方仓库。

+ [如何使用vscode看Linux源码](http://119.23.219.145/posts/linux-kernel-linux-%E6%BA%90%E7%A0%81%E9%98%85%E8%AF%BB%E5%B7%A5%E5%85%B7%E5%92%8C%E8%B5%84%E6%96%99/)
  1. vscode需要添加插件 **C/C++ GNU Global* 及 C++/c 扩展

+ [为什么总是技术人容易被踢出局](https://www.zhihu.com/question/327063112/answer/2121708882)
  1. 技术人忙于技术，劳心者治于人，劳力者治于人
  2. 资本进来后，共患难容易，同甘难。对于技术人员，入司前谈好待遇、分红、参股，不要觉得自己做出成果后，公司自然会给。这就好像律师费一样，打官司之前谈妥，那是你的功劳你的酬劳；打完官司之后谈，那是我本来就能赢。

2022.2.23
-----------
+ [Docker在走下坡路吗](https://mp.weixin.qq.com/s/pdmDjdQNbYC6g1EKI1zlcA)
  1. Docker作为一家初创公司，技术创新，横空出世，降维打击传统技术，受到开发者热棒，但是过了仅仅数年，就被Google巨头给干爬下了。
  2. 在kubernetes刚推出及未推出来还不成气候的时候，还依赖于Docker运行时。Docker也曾一度达到热度极点，可当Kubernetes壮大后，推出了自己的rkt运行时，之后设定cri标准，docker将自已的核心放在了containerd，为了适配自己又推出docker-shim垫片，最后架构越来越来长，docker只剩下了一个空壳，谁都可以去替换它，就没落了。


2022.2.22
---------
+ [mediafire](https://app.mediafire.com/myfiles)在国外是最常用的文件共享工具，其次才是google drive及one drive。该工具支持匿名上传文件，生成一个免费的对象存储地址供他人下载。如我[上传的文件](https://www.mediafire.com/view/bipzo9egshhkrhd/gaitubao_Zack%25E4%25B9%258B%25E4%25BA%2591%25E5%258E%259F%25E7%2594%259F_%25281%2529.png/file)
+ [bypass-paywalls](https://github.com/iamadamdev/bypass-paywalls-chrome)是一个浏览器插件，通过名字理解其意思“绕过付费墙”。外媒网站如果要看一般都需要你去登陆，根据付费情况，有浏览次数的限制（如medium就是如此）。该插件就是扫除这些障碍，无需登陆，任意数量的浏览。

2022.2.18
----------
+ [`zlib`](https://zh.wikipedia.org/wiki/Z-Library)是最大的免费在线图书馆，简单来讲，最大的盗版商，支持个人用户上传，接受捐赠。对于个人来讲，可以[免费下载很多的在线电子书](https://zh.cn1lib.org/)，真香。

+ [跑步电台](https://justinyan.me/post/category/podcast)，听了一期，声音很清晰，主持人谈吐听得很舒服

+ [三年负债从从8万变80万](https://www.v2ex.com/t/833951#reply40)，别人的是故事，如果是自己身上就是事故。我所理解的创业是水道渠成的事情，而不是为了创业而创业。好比一切都已经准备好，觉得有必要真正的光明正大的开始以此为业，并有稳定的收入，可持续发展，才正是挂牌当老板。

2022.2.15
----------
+ [`莱布尼茨`](https://zh.wikipedia.org/wiki/%E6%88%88%E7%89%B9%E5%BC%97%E9%87%8C%E5%BE%B7%C2%B7%E8%8E%B1%E5%B8%83%E5%B0%BC%E8%8C%A8)受中国《易经》八卦的启示，发明了二进制，推动了计算机的发明。另外还发明了微积分。

2022.1.18
----------
+ [gore是go命令行式编程二进制命令，类似于ipython](https://github.com/x-motemen/gore)
  1. 安装：`go install github.com/x-motemen/gore/cmd/gore@latest`
  2. 可以自动导入包
    ```
    ✗ gore --autoimport
    gore version 0.5.3  :help for help
    gore> fmt.Println("hello world")
    hello world
    12
    nil
    gore>
    ```
    
+ `go mod why`可以查看为什么需要引用这个包
  ```bash
  go mod why -m  k8s.io/client-go
  # k8s.io/client-go
  csm-apiserver/third-party/kiali/kubernetes/cache
  k8s.io/client-go/informers
  ```

2022.1.12
----------
+ [斯坦福大学关于区块链的公开课](https://cs251.stanford.edu/syllabus.html)

+ 参考该[blog](https://blog.leeyom.top/#/posts/38)，将自己的博客系统在支持原有的Jeklly基础上，增加通过issue打label的功能来自动发布博客文章。

  

2022.1.11
----------
+ [json marshal与encode的区别是什么？](https://stackoverflow.com/questions/33061117/in-golang-what-is-the-difference-between-json-encoding-and-marshalling)
  1. `marsha`是go的type(如：struct)换换成json;`encode`是先换go的type(如stuct或是map)先marshal成json，然后转换成http可以传输的buffer数据。


2022.1.9
---------
+ 2022年已经开始了近10天，做了以下几件事
  1. 正式成为了UP主，bilibili的[Kubernetes源码分享](https://www.bilibili.com/video/BV1wm4y1D7XV)视频大受欢迎。
  2. 正式开始关注健身与煅炼，博客上线了[个人跑步分享的网站](https://running-page-puce.vercel.app/)。
  3. **着重关注理财，培养财商。**
    - 在读`《穷爸爸富爸爸》`，深受触发。
    - [关于中国楼市、股票相关的一些热贴](https://github.com/xiantang/kkndme_tianya)。
    - [如何不用努力工作也能赚取财富](https://amaca.substack.com/p/how-i-got-wealthy-without-working)
    - [KK讲长尾效应](https://service.goodcharacters.com/blog/blog.php?id=165)
      1. 从事创作或艺术的人，只要1000个忠实的粉丝就能维持生活。
      2. [如何不努力也能财富自由](https://geekplux.zhubai.love/posts/2089594064939798528)

+ [Gopher的阅读清单](https://github.com/qichengzx/gopher-reading-list-zh_CN)
  1. 讲了Go的编程，含基础篇、中级篇、高级篇。
  2. 包含一些优秀的博文。

+ [CookShell左耳朵耗子讲加密](https://geekplux.zhubai.love/posts/2089594064939798528)

--------------------

2021.12.31
-----------
+ [计算机教育中缺失的一课](https://missing-semester-cn.github.io/)
  1. 作为计算机科学从业人员，需要尽早知道并熟练掌握这些工具及方法的使用
  2. 由MIT发布的公开课，全程英语，可以练习听力

+ [浙江大学一硕士毕业半年社招历程](https://github.com/conanhujinming/tips_for_interview/blob/master/After_Half_A_Year.md#%E4%BD%93%E9%AA%8C%E6%8E%88%E8%AF%BE%E7%94%9F%E6%B4%BB)

+ [名校公开课](https://github.com/conanhujinming/comments-for-awesome-courses)

+ [浙江大学的课程](https://github.com/QSCTech/zju-icicles)

2021.12.29
------------
+ [普林斯顿大学算法的公开课](https://www.coursera.org/lecture/algorithms-part1/dynamic-connectivity-fjxHC)
  1. 动态连接
  2. 快速查找

2021.12.10
--------------
+ [限流器之令牌桶基本原理](https://gocn.vip/topics/20824)
  1. 限流有两种方式：令牌桶和漏桶。
  2. 它们的区别是什么？
  3. 漏桶速率一定，只能一直按这个速率来处理。
  4. 令牌桶，如果桶中的令牌有100个，瞬间来了100个请求，这些请求每个都能获得一个令牌，同时被处理，令牌被消耗完。



-------------

2021.12.9
--------

+ [深入浅出ebpf](https://www.ebpf.top/post/head_first_bpf/)
  1. 该ppt源自于talkgo社区的分享。
  2. ebpf是什么、应用场景、与go的关联。
  3. 使用tc控制流量，钩子函数

-----------------

2021.12.5
----------

+ [图灵与冯诺依曼不为人知的故事](https://www.eet-china.com/mp/a63731.html)
   1. 二人都为计算机的发明做出了不小的贡献，起因都跟军事有关。图灵是二战时英国为了破译纳粹密码，冯是曼哈顿原子弹计划。
   2. 图灵人工智能之父，因同性恋被化学阉割，40多岁死于自杀。死时床前有一个被咬一口的苹果。乔布斯在创立公司时，因此命名。图灵是运动达人，爱好跑步。
   3. 人工智能电影《机械姬》。
   4. 《模仿游戏》，讲述了“计算机科学之父”艾伦·图灵的传奇人生。
   5. 计算机的发明：电子管、晶体管、集成电路、大规模集成电路。
   6. 21世纪人类改变世界的三大工程：曼哈顿原子弹、阿波罗登月、基因工程（中国参与）。

-------------

2021.11.30
-----

+ [开发和调试wasm filters](https://events.istio.io/istiocon-2021/slides/c4p-DevelopingDebuggingWasm-Idit-Yuval.pdf)。istio背后的商业公司是solo.io，这篇文章是istiocon-2021上的分享ppt，其实也是宣传solo.io的mesh。
+ [重新定义代理的扩展性：envoy/istio引入wasm](https://istio.io/latest/zh/blog/2020/wasm-announce/)
  1. 什么是wasm？
  2. 什么是ABI？

2021.11.25
---------

+ [servicemesh与ebpf的结合](https://thenewstack.io/how-ebpf-streamlines-the-service-mesh/)
  1. eBPF是servicemesh的趋势，一个节点上所有的容器共享一个内核，因此只用将node节点设置为ebpf模式，就无需每个pod新增一个sidecar envoy代理，未来趋势sidecarless。
  2. Cilium插件提供eBPF特性，istio使用该cni。
  3. 由于无需经过sidecar，会有性能提升。

+ [dns解析详解](https://linuxgeeks.github.io/2016/04/11/110119-CentOS%E4%B8%ADresolv.conf%E7%9A%84%E9%85%8D%E7%BD%AE%E5%AE%9E%E9%AA%8C/)。`/etc/resolv.conf`中的记录一般不要超过3个，否则会有莫名其妙的问题。比如`ping`、`dig`只会查前三个，而`curl`会轮询所有的。

+ [性能分析与调优](https://drive.google.com/file/d/13-67AlCk9iToRIvWhJ9GP6KRqEN4dFZe/view?usp=sharing)，这个文档是个加密文件，涉及一些不便公开的因素，是某公司顶极大牛分享的文档。概括了常见的调优及性能指标，需要的可以私信。
  - top 各指标的意思，如si,sy,ni,id,wa,hi,si 等。
  - 如何通过 top 指令判断主机是io高还是网络负载高。
  - 如何测速，如何调优，如何测延时，如何分析存储性能，如何使用fio测试硬盘吞吐，如何看kernel调用栈cpu用量，如何直接在Linux上编写脚本直接查出包的转发率（pps）。
  
-----------------------

2021.11.23
---------

+ [元宇宙](https://drive.google.com/file/d/1OfskptDEHQWvipMtvcNBufO3FGicAfjZ/view?usp=sharing)市场分析，《头号玩家》电影是其情景的描述，用户沉浸式。

+ [Linux capabilities 权限控制](https://man7.org/linux/man-pages/man7/capabilities.7.html)有41种，root权限最大，可以通过控制cap去掉某些权限。~~kubernetes通过[podsecuritypolicy](https://kubernetes.io/docs/concepts/policy/pod-security-policy/)来控制~~。PSP已经被废弃，直接使用pod的[security context](https://kubernetes.io/docs/concepts/policy/pod-security-policy/)来控制。
`securityContext.capabilities.add ([]string)`，如Istio-proxy container中的yaml
  ```yaml
  securityContext:
    allowPrivilegeEscalation: false
    capabilities:
      drop:
      - ALL
    privileged: false
    readOnlyRootFilesystem: true
    runAsGroup: 1337
    runAsNonRoot: true
    runAsUser: 1337
  ```

-------------

2021.11.22
-----------------

+ [二进制瘦身命令](https://github.com/upx/upx/releases)，一个二进制文件近60M，瘦身后只剩14M，瘦身效果减小70%多，原二进制文件还可以使用不受影响。

  ```bash
  [root@i-b2e6autw bin]# du -sh ./docker
  59M	./docker
  [root@i-b2e6autw bin]# /tmp/upx ./docker
                        Ultimate Packer for eXecutables
                            Copyright (C) 1996 - 2020
  UPX 3.96        Markus Oberhumer, Laszlo Molnar & John Reiser   Jan 23rd 2020

          File size         Ratio      Format      Name
    --------------------   ------   -----------   -----------
    61854216 ->  14345912   23.19%   linux/amd64   docker

    Packed 1 file.
    [root@i-b2e6autw bin]# du -sh ./docker
    14M	./docker
    [root@i-b2e6autw bin]# docker --help
    Usage:  docker [OPTIONS] COMMAND
    A self-sufficient runtime for containers
    ...
  ```

+ 可以直接在 github releases 中下载编译好的二进制，且可以根据平台的架构直接下载对应的二进制，如[Dockerfile](https://github.com/istio/tools/blob/master/docker/build-tools/Dockerfile#L193)中写法 `ADD https://github.com/upx/upx/releases/download/v${UPX_VERSION}/upx-${UPX_VERSION}-$(uname -m)_linux.tar.xz /tmp`，注意 github release url中的格式：`https://github.com/username/reponame/releases/download/$version/$releasename`。linux安装用二进制，Mac安装：`brew install upx`。

+ [su-exec](https://github.com/ncopa/su-exec)是一个二进制命令，挂载到容器中执行，可以提权至root。[Istio官方利用su-exec获取到1号进程root的权限，因此拥有root的所有权限。在创建容器时挂载主机目录到容器中，获取到docker登陆的凭证，并在主机上创建一个用户把自己的uid给system权限](https://github.com/istio/tools/blob/master/docker/build-tools/docker-entrypoint.sh#L52)。
  ```bash
  # Add user based upon passed UID. this means Istio need no longer host mount /etc/passwd
  # nor /etc/group
  su-exec 0:0 useradd --uid "${uid}" --system user # 此时在host上已经创建了一个系统用户user
  ```

----------

2021.11.21
-----------

+ 从事软件行业，有大量的技术网站收藏，在`收藏等于掌握`的今天，碎片化的知识如何有效的组织管理，并在需要的时候有效的检索尤为重要。 
  1. [软件工程师如何组织你并管理你的知识](https://dev.to/brpaz/how-do-i-organize-my-knowledge-as-a-software-engineer-4387)，根据推荐，目前在使用的有Notion跟Diigo。
  2. [管理个人知识](https://tkainrad.dev/posts/managing-my-personal-knowledge-base/)，同样提到了Notion。

  **每个人管理自己的知识有各自的方式，我几乎使用过市场上流行的所有的笔记类软件。如：使用的第一款软件，`印象笔记`，至今仍在使用，虽然广告及搜索一直被诟病，国内网络环境，加上微信的剪藏好用；当然有历史资料的遗留原因。`有道笔记`及`OneNote` `石墨文档` 都差不多，石墨文档的多人协作优势明显。`Notion`国外流行，界面清新，搜索功能强大，编辑器功能丰富，可以像使用写代码一样写笔记，直接兼容Markdown，这也是我一直使用的原因，并且可以直接把笔记分享成blog。`灵雀`国内小清新，可以一键分享到网上，使用笔记功能写博客，但是不够稳定。用了这么多，最好的其实还是Github的issue管理，或是Gist。Gist可以提供版本管理，并且可以直接使用Github的命令行工具gh来编辑。笔记类软件强调的知识管理，最重要的是记录、检索、共享存储，目前我使用的方式是：Github Issue(归档类知识，读书摘录) + Gist (配合命令行) + Notion(草稿，记录每天日常) + Diigo（网页划线，及时保存） + Google Docs (画图，调研类，替代word) + 印象笔记（微信文章剪藏、日记、国内网络环境下需要保存临时的文档及想法等）。自己钻研的一些技术，会形成Blog记录。**

  总而言之：知识管理最重要的是`共享`（一定不能只保留在终端），每个人的使用习惯不一样，不要盲目模仿，适合自己的才是最好的。`重要的是知识的内容，而不是形式`。

+ [servicemesh的深层次原理](https://cloud.tencent.com/developer/article/1373882?from=article.detail.1727679)
  1. 任何软件架构设计，其核心都是围绕数据展开的，基本上如何定义数据结构就决定了其流程的走向，剩下的不外乎加上一些设计手法，抽离出变与不变的部分，不变的部分最终会转化为程序的主流程，基本固化，变的部分尽量保证拥有良好的扩展性、易维护性，最终会转化为主流程中各个抽象的流程节点。对于Envoy也不例外，作为一个网络代理程序，其核心职责就是完成请求的转发，在转发的过程中人们又希望可以对其做一定程度的微处理，例如附加一个Header属性等，否则就没必要使用代理程序了。那么Envoy是如何运作的呢？它是如何定义其数据结构，并围绕该数据结构设计软件架构、程序流程，又是如何抽象出变得部分，保证高扩展性呢？
  2. dpdk的实现原理：由于传统的数据包的处理是在linux内核中处理的，应用在数据交换时，会先到内核态(iptables、接收数据等) -- 用户态 （应用） -- 内核态（发送数据等），频繁用户态、内核态的切换，消耗资源。DPDK将在内核态处理的过程，全部转换到用户态来处理，绕过linux协议栈，减少上下文的切换。
  3. 与DPDK的内存全部在用户空间来避免内存拷贝、上下文切换、系统调用等不同，eBPF都是在内核空间执行的。但两者的核心都是通过避免数据包在内核态与用户态之间的往复来提升转发效率。ebpf与dpdk处理的方式恰恰相反，dpdk将包的处理放在用户态，而ebpf将包的处理过程放在linux内核态。
  4. xdp使用ebpf功能，去掉了队列的缓存，转发更快。
  5. 简说QUIC协议，说完了如何高效的转发请求，接下来我们聊聊Sidecar之间如何高效的通讯。提到通讯那就一定要提及通讯协议了，作为我们耳熟能详的两大基本通讯协议TCP与UDP的优缺点这里就不再赘述了，那么我们是否能整合TCP与UDP两者的优点呢，这样既保证了TCP的可靠与安全性，又兼具UDP的速度与效率，不可否认的是往往正是出于人们对美好事物的向往，才持续不断的推动我们前进的脚本。QUIC在这种期许下诞生，旨在创建几乎等同于TCP的独立连接，但有着低延迟，并对类似SPDY的多路复用流协议有更好的支持。
  6. tcp与udp有各自的优劣点，udp快但不安全，tcp安全但慢，quic就是为了解决这种场景而生的，即又快又安全。QUIC协议的诞生就是为了降低网络延迟，开创性的使用了UDP协议作为底层传输协议，通过多种方式减少了网络延迟。因此带来了性能的极大提升，且具体的提升效果在Google旗下的YouTube已经验证。
  7. envoy V1使用http通讯，V2使用grpc。envoy的架构是`多线程`+`非阻塞`+`异步IO（libevent）`。envoy由于的通信还是基于常规的收发包过程，数据包的处理会经过 `内核` - `用户态` - `内核`的过程，社区正在寻求与ebpf的结合，减少频繁上下文的切换；另外，社区也正在实现使用quic协议，来提高性能。
  8. 目前servicemesh的行业里面，envoy及istio是明星，但由不少竞争者。跟envoy同一维度的有nginx、Haproxy，蚂蚁基于istio，但是使用mosn替换envoy。servicemesh的概念由Linkerd提出，但是被Google、Lyft、IBM大厂的istio抢了风头。Linkd使用scala语言，运行在Java虚拟机上。Conduit跟Linkd同属于Buoyant公司，Conduit脱胎于Linkd，Rust做数据面，Go作控制面，一个产品同时包含控制面跟数据面，不像envoy及linkd只是数据面。推出Conduit来抢夺Istio的市场。NginxMesh目前已经基于废弃，但是当初是使用istio+nginx来实现，使用nginx来替换envoy。

+ c++学习资料
  1. [github上c++学习资料](https://github.com/0voice/cpp_new_features/blob/main/C%2B%2B%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8.md)
  2. [c++那些事](https://github.com/Light-City/CPlusPlusThings)
  2. [Youtube上c++学习资料](https://www.youtube.com/playlist?list=PLg4AoophFZWZ7Llifowo-1WGMVICq-mfw)
  3. [C++ primer](https://www.oreilly.com/library/view/c-primer-fifth/9780133053043/)

+ [每天写代码](https://johnresig.com/blog/write-code-every-day/)
  1. 这个blog的作者是jquery的创造者，该blog讲述了为什么他要坚持每天写代码。
  2. 他给自己规定每天写代码的原则：
    - 不只提交格式化的代码
    - 不只提交缩进的代码
    - 不因为要写代码而牺牲睡眠时间
    - 所有写的代码全部开源

---------------

2021.11.16
----------

+ 思考：`为什么愿意把自己的知识分享出来？不怕同行超越么？`  
  1. 三十岁之前确实不愿意分享，自己知道的东西不愿意告诉别人，根本原因有两点：
    - ~~保持自已神秘感，隐藏自己，不过多暴露。这样别人不知道你的水深水浅，自然也不会太轻视你。~~
    - ~~自己会的就那么几招，让别人学会了，自己就没有优势了。~~
  2. 三十岁之后，无形中有了紧迫性。00后步入了职场，仿佛看到了当初了自己，现在不可避免的成为了前辈。如何保持核心竞争力？惟有更Open、更多参与开源、技术分享，三人行必有我师，问渠哪得清如许、惟有源头活水来。终生学习，多交流，思想多碰撞，才能撞出火花。
  3. 知识是开源的，不用保留。尽之力度人，若他人有所收获，会从内心认同。能被轻易学去的知识，算不上核心技术。
  4. 真正的有心人，你教与不教，他们迟早会出来。我们的努力的地方，不是降低别人的学习速度，而是提高自己的技术水平。
  5. 若能形成技术影响力，认识一帮志同道合的人，也许是以后自己职业生涯中的合作伙伴，可以一起搞点事情，是隐形的资产。

+ [计算机科学（Computer Science）的学习资料](https://github.com/spring2go/cs_study_plan)
  1. 对于从事计算机编程的人员来说，基本功尤其重要。工作十年并不一定拥有十年的工作经验，可能只是一个技能使用了十年而已（CURD）；
  2. 《深入理解计算机系统》非常值得仔细研读，链接中给出了学习资料，同步B站视频；

+ [关于 kubernetes 中的 resync 的讨论](https://github.com/kubernetes/kubernetes/pull/24142)
  1. resync 是同步 etcd 中的数据到 local cache; 一般控制器需要设置为0，即永远不做 resync, 资源的变化通过 informer -> FIFO Quene; FIFO 会做两件事，更新本地 local cache，通知 controller reconcile 达到资源描述的状态（Work Quene）；
  2. 早期有 resync 与 relist 的区别，该 issue 中讨论是否要将它们分开；主要原因是每 30s 做一次资源的 resync 消耗资源厉害，并做了火焰图分析；
  3. Get List 从本地 local cache 获取数据，减少缓存；

------------------

2021.11.7
---------

+ [公司内部如何处理文档，Google Twitter Spotify](https://blog.doctave.com/2021/09/07/how-google-twitter-and-spotify-build-culture-of-documentation.html) 
  1. 在KubeSphere工作时，Doc 的处理就是一件非常让工程师头痛的问题。在发版前期，缺少大量的文档，会集中所有开发人员写文档，开发人员其实并不擅长也不乐意写文档。直到招聘了专职文档工程师才有所改善。因此文档工作确实需要当作一件事情来认真对待。
  2. 有些文档在github wiki，有些在 confluence， 有人喜欢用word, 也有人会喜欢使用 google doc，甚至有人直接放在代码注释里面。文档还有时间有效期。有效搜索降低，当一篇文章命中率不高时，其价值会大打折扣。曾经在青云 iaas 组工作过，内部有 bbs 技术贴（类似内部技术论坛），会记录很多故障处理方式，及当时排查思路，几年前的文章都会被搜索借鉴，当与现在有出入时，可以直接评论修改，有时间记录。觉得是一个很好的处理方式。
  3. google 内部研发了一个系统，所有的人员使用同一套，杜绝了内部文档混乱的形式。

--------

+ [90岁台积电创始人张忠谋1.5小时的演讲内容，都是干货](https://zhidx.com/p/301575.html)
  1. 需要区分商品与客制品，只要有两家公司在做同一个产品，它就是商品，价格取决于竞争对手；做定制化需求，只有你一家在做，那就是客制品，利润更高。要多做客制品。
  2. 利润 = 价格 - 成本。
  3. 台积电并不是一个半导体公司，为什么？如果台积电是一个半导体公司，那么它的客户应该是计算机行业。但是台积电的定位是客户是半导体，所以台积电是一个半导体服务公司，即晶圆制造。
  4. 看一个公司具体是一个什么公司，需要从客户分析。如：我们经常使用谷歌，但是我们并不付费，谷歌怎么挣钱呢？因为我们并不是谷歌的客户，我们只是它的工具，给它提供数据。它的客户是广告商。星巴克的客户是谁？星巴克的客户定位并不是啥喝咖啡的人，而是懂得享受生活的人（免费wifi，场所）。

----------------------

+ [Rob Pike关于 go 语言演讲的 ppt](https://talks.golang.org/2012/concurrency.slide#1)  
    1. 并行与并发的区别，并发是cpu的分时调用，并行是多核cpu同时工作；
    2. go 的 select 与 switch 的区别；select 与 switch 很像，但是select中的每一个case 均是一个 chan；
    3. go 的 chan 设计机制，利用通信来共享内存，而不是通过共享内存来通信；
    4. go 的 chan 与 goroutine 的使用，通过 goroutine 来开启协程实现并发，使用chan 来保持 协程异步通信；
    5. 定义一个函数，传入一个对象，返回值同样是一个函数，实现了装饰器的作用
    ```golang
    func fakeSearch(kind string) Search {
        return func(query string) Result {
          time.Sleep(rand.Intn(100)) * time.Millisecond)
          return Result(fmt.Sprintf("%s result for %q\n", kind, query))
        }
    }
    ```
    6. go 的并发有优势，如何控制并发？有两种方式：通过 chan ，通过 select 来控制。select 如果没有 chan 输入，要么走 default ，如果没有 default 就一直等待 chan 准备好。通过两种方式分别实现相同的逻辑
    ```golang
    func fanIn(input1, input2 <- chan string) <-chan string {
      c := make(chan string)
      go func(){ for { c <- <-input1}}()
      go func(){ for { c <- <-input2}}()
      return c
    }
    ```
    通过 select 实现
    ```golang
    func fanIn(input1, input2 <- chan string) <- chan string{
      select {
        case c := <-input1:
          return c
        case c := <- input2:
          return c
      }
    }
    ```
    7. slide 中有很多经典代码值得一看。  


---------------------------

+ [为什么为了面试谷歌每天8小时全天侯的学习](https://www.freecodecamp.org/news/why-i-studied-full-time-for-8-months-for-a-google-interview-cc662ce9bb13/)

  一个15年的web开发者，已经开了三个公司，当过CEO，做过产品管理、设计、市场，觉得自己并不是一个软件工程师。
花了几个月，阅读了大量的技术书籍、技术视频、研究了算法，只为能成功应聘上Google。
  书中有几个观点值得学习：
  1. 不要什么知识都学。不要盲目学习，任何一个细节如果深挖都是极其复杂的知识，千招会不如一招熟，要想在某一方面有所成就，需要成为某个特地领域的专家。精力有限，什么都学、注定什么都不可能太深入。
  2. 谷歌招聘最优秀的人才，也非常重视人才的培养，有一整套成熟人才管理方法，能给予足够的回报。如果能进入谷歌工作，那么也能满足其他公司要求。所以作者将目标定在了谷歌。
  3. 即使作者已经取得了一系列成就，但是是仍愿意以一个初级软件工程师的身份进入公司，是明白了“Web开发者”与“软件工程”的区别，并且中年危机所致（担心自己的核心竞争力不足）。
  4. 作者非常详细的讲述了自己的所思所想，并把自己的学习资料放在了[Github](https://github.com/jwasham/coding-interview-university#book-list)。由于之前从事私有产品开发，自己的Github 中并没有什么内容。但是这个内容放在了Github上后，没有几天就上了Github Trending，并且很快Star就破万。

----------------------

+ 19世纪加州淘金热中最赚钱的是谁？99%的淘金者都没有挖到金子，真正赚钱的是那些卖给淘金者铁锹的人
+ 几只猴子共同抬一块石头，其中一只猴子想，即使我不用力，他们也会抬走的，于是他悄悄松了手。不料其他几只猴子也作如是想。后果可想而知：石头掉下来，砸伤了所有的猴子。
+ 崇祯年间，天下鼎沸，关外女真渐成气候，关内民军势成燎原，崇祯像个救火队长一样忧心如焚，最终仍免不了做个励精图治的亡国之君。究其原因，固然有大厦将倾、独木难撑之窘，但其时国家财政之捉襟见肘和以崇祯为代表的高级官员们的集体哭穷似乎更有直接关系。
+ 对付坏人，需要手段，须得有颗沉静的心。不能钻牛角尖，不可以赌气，不能闹情绪。要做到救得了患伤，打得过流氓。上得了讲堂，翻得过围墙。掐得起群架，哄得住婆娘。震得住医闹，迷得死女王。能斥退厚颜无耻的无知蠢货，能说服学富五车的专家大师。玩得了小清新，能让投资人见之倾心。秀得了下限，可让觊觎者知难而退。你想做个好人，就得比坏人更聪明，比恶人更强。无论你是想救死扶伤，还是做点其它什么有意义的事情，你这一生，总难免要与人性的晦涩打交道。第四：所谓创业，只为明理。不要为了钱而创业，要为了深悟智慧而做事。那么你就会发现，所谓创业的法则，不过是人性的规律。你若不能操控人心，必为人心所操控。--老雾2018.2.2
+ 中庸是一种平衡和控制，平庸则是笨蛋
+ 对于时间，每个人的反应速度不一样，因此对于同一秒，感受到的时间长度不一样，因此我们要做的是提高自己的反应速度，以达到延长生命之目的
+ 现实生活中有太多的人将手段与目的搞混了，教管下级成了乐趣，促进工作反而处在了次要位置
+ 岂能尽如人意，但求无愧于心 -<侯卫东官场笔记>
+ 不向静中参妙理,纵然颖悟也虚浮 立乎其大 和而不同 古之成大事者，不惟有超世之才，亦必有坚韧不拔之志
+ 下愚莫揣上智，处于本能阶段的人，因其视野闭塞，会以为所有人都在这个层级。会震惊于不同观点的出现，认为对方脑子有病。 ---   《老雾》
+ 当你读书多了，收入高了，修养高了，那么继续读；如果读书多了，脑子二了，凡事儿都较真，人见人恶心的话，就没必要读书了。多读书没错，脑子不好才有错。
+ 看的风景多了便会发现，成功的人多半态度谦卑，倨傲的人往往并无什么实质，发财的人声音都比较小，声音大的都在琢磨四处想办法去圈点什么钱。大家都觉得自己是聪明的，成功的人却往往觉得自己还不够聪明，还要四处学习。而想占便宜的人则到处流窜，也就是所谓的社会活动家，在各种社群或者线下聚会中找到自己的存在感，这的确是一种非常好的麻醉自己的办法
+ 有些人很聪明，你跟他说怎么做，他非要跟你辩论；有些人比较笨，你跟他说怎么做，他就简单执行，然后适当优化。结果往往这些比较笨的人赚到钱了，而那些聪明人还在那辩论呢
+ 创业不是一个人的事情，要用你的智商去选择一个合适的项目，要用情商去建立一个核实的团队，用胆商去体味一个叫做创业家的精神。创业的成功是偶然的，创业的失败是必然的，无论你做了多少的准备，一个微小的事情就能够毁掉你的一切努力
+ Good tools make application development quicker and easier to maintain than if you did everything by hand  --FROM  angularJS-office-site

