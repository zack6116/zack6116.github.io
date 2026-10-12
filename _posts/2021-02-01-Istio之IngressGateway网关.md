---
layout: post
published: true
title:  Istio之IngressGateway网关
categories: [servicemesh]
tags: [istio,kubernetes]
---
* content
{:toc}

# 为什么需要Istio Ingressgateway?

Kubernetes 中有一个概念叫Ingress。Ingress资源定义了入口到集群服务的路由规则。  
通过定义网络规则和策略，来对入口的流量进行过滤及路由的分发。  
如果流量未经过这个入口，则禁止流量访问集群内的任何服务。如果入口点允许流量进入，则对这些流量代理到合适的节点。

Ingress是K8s的概念，Istio有一个类似的对应资源，称之为Gateway。

Kubernetes默认使用Nginx Ingress，为什么istio需要有Istio-Ingress Gwateway组件？具体增强了哪些功能？

kubernetes-sig开发了Nginx Ingress控制器，只能代理HTTP流量。而Istio-Ingres Gateway可支持多种协议如gRPC、HTTP、TLS、TCP等。

**Nginx Ingress只处理7层流量，因此只能支持Http。Istio gateway处理4～6层流量，因此可以对不同的协议做更精细化的控制；然后把7层的流量处理全部交给Virtualservice来处理。**

问题：可以使用Istio-ingressgateway代理非servicemesh的服务吗？如果istio gateway不能代理七层流量，为什么又说比Ingress功能强大呢？
  
可以代理未注入sidecar的服务，virtualservice可以生效（ingress-gateway pod envoy的规则）。

# httpbin负载介绍

本实验将使用httpbin负载做为后端，httpbin是一个使用python开发的应用程序，基本上将http涵盖了所有的http的操作。如获取响应请求头：

```bash
# curl 10.233.27.244:8080/headers -Hhost:zack6116.github.io
{
  "headers": {
    "Accept": "*/*",
    "Content-Length": "0",
    "Host": "zack6116.github.io",
    "User-Agent": "curl/7.29.0",
    "X-B3-Sampled": "0",
    "X-B3-Spanid": "bf0275d010c038fb",
    "X-B3-Traceid": "50ab410956a362fbbf0275d010c038fb"
  }
}
```

注意上面给它传入Host头，与H连着写，且host是小写，但是在请求中，其实是自动被转为大写的。

获取源地址IP：

```bash
# curl 10.233.27.244:8080/ip
{
  "origin": "127.0.0.1"
}
```

# 部署负载及Gateway

使用Gateway指定暴露端口，HTTP或是HTTPS重定向，或是指定协议及端口等。

创建负载httpbin

```bash
kubectl -n test-gateway create deploy httpbin --image kennethreitz/httpbin --port 80
kubectl -n test-gateway expose deploy httpbin --port 8080 --target-port 80
```

注入sidecar，将deployment的template加上annotations `sidecar.istio.io/inject: "true"`

创建Gateway

```bash
kubectl create -f - <<EOF
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: httpbin-gateway
  namespace: test-gateway
spec:
  selector:
    # 匹配Pod Label，指定Gateway Controller
    istio: ingressgateway
  servers:
  # port中定义的端口，为istio-ingress的pod端口（注意非svc），其实是envoy反向代理的入口端口
  # 定义port后，在istio-ingress pod中会开一个它的监听端口
  - port:
      # 注意这个number，端口为ingressgateway svc的众多端口中的一个 （这个说法不对，svc众多端口只是服务端口，而这里定义的是targetPort）
      # 在ingressgateway ingress 的svc中，自定义端口，需要符合形如 "name: http-xxx"的规则
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "zack6116.github.io"
EOF
```

> 需要区分gateway中定义的port与istio-ingress svc的端口。
> istio-ingress svc只是映射了svc port（服务端口） 与 target port(pod的端口)。
> gateway spec.port是pod中的监听端口

创建virtualservice

```bash
kubectl apply -f - <<EOF
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: httpbin
  namespace: test-gateway
spec:
  hosts:
  - "zack6116.github.io"
  gateways:
  - httpbin-gateway
  http:
  - match:
    - uri:
        prefix: /status
    - uri:
        prefix: /delay
    route:
    - destination:
        port:
          number: 8080
        host: httpbin
EOF
```

暴露istio-ingressgateway为NodePort或是使用LB，使用外部流量进入。获取ingress IP PORT

```bash
kubectl -n istio-system get svc  | grep istio-ingress

istio-ingressgateway        NodePort    10.233.39.106   <none>        15021:31646/TCP,80:30084/TCP,443:32740/TCP,15443:30534/TCP   18d
```

访问：

```bash
curl -I  http://192.168.0.13:30084/status/200 -H 'Host: zack6116.github.io'
HTTP/1.1 200 OK
server: istio-envoy
date: Mon, 08 Feb 2021 08:14:49 GMT
content-type: text/html; charset=utf-8
access-control-allow-origin: *
access-control-allow-credentials: true
content-length: 0
x-envoy-upstream-service-time: 2
```

去掉Host后就不正常。

```bash
curl -I  http://192.168.0.13:30084/status/200
HTTP/1.1 404 Not Found
date: Mon, 08 Feb 2021 08:20:53 GMT
server: istio-envoy
transfer-encoding: chunked
```

如果不想受Host的约束，可以把Gateway跟Vitualservice的Host都改为通配符'*'。

Host中的对应的值，是否需要用双引号括起来，都行，不受影响。

如果无论怎样修改virtualservice或是gateway，都能得到结果，导致跟预期不符。

很有可能是在其他的namespace还有gateway或是Ingress导致的这个端口被重复定义。

此时引入另一个问题：是否一定需要httpbin这个后端服务注入sidecar才能使用ingress gateway把virtualservice中的路由把流量导过来？

答案：不是。httpbin不用注入sidecar，virtualservice中定义的规则一样生效。原因是：ingressgateway本身是一个envoy，定义的virtualservice会在这个envoy上生效，相当于是一个Loadbalance的角色。那么什么时候要求httpbin注入sidecar呢？答：当这个服务需要访问别的服务，需要对别的服务定义virtualservice的时候。另外，如果这个pod注入sidecar后，可以更好的对这个pod中进入的流量及请求度量。

# 一个更多规则Gateway示例

由于上面的Gateway及Virtualservice其实很简单，这里给一个更丰富的demo

```bash
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: httpbin-gateway
  namespace: test-gateway
spec:
  selector:

    istio: ingressgateway
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "zack6116.github.io"
    tls:
      # 把http://xxx.com 转成 https://xxx.com
      httpsRedirect: true # sends 301 redirect for http requests
  - port:
      number: 443
      name: https-443
      protocol: HTTPS
    hosts:
    - "zack6116.github.io"
    tls:
      mode: SIMPLE # enables HTTPS on this port
      serverCertificate: /etc/certs/servercert.pem
      privateKey: /etc/certs/privatekey.pem
  - port:
      number: 9443
      name: https-9443
      protocol: HTTPS
    hosts:
    # host匹配从指定namespace发过来的请求
    - "test/*.bookinfo.com"
    tls:
      mode: SIMPLE # enables HTTPS on this port
      # 密钥存在k8s secret资源中
      credentialName: bookinfo-secret # fetches certs from Kubernetes secret
  - port:
      number: 9080
      name: http-wildcard
      protocol: HTTP
    hosts:
    - "*"
  - port:
      number: 3306 # to expose internal service via external port 2379
      name: mysql
      protocol: MYSQL
    hosts:
    - "*"
```

创建数据负载 mysql

```bash
kubectl -n test create deploy mysql --image mysql --port 3306
```

修改deployment，注入sidecar，增加变量

```bash
 kubectl -n test edit deploy mysql
 
    templates:
      ...
      annotations:
        sidecar.istio.io/inject: "true"
    spec:
      containers:
      - env:
        - name: MYSQL_ROOT_PASSWORD
          value: "123456"
```

创建virtualservice

```bash
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: httpbin-vs
  namespace: test-gateway
spec:
  hosts:
  - "zack6116.github.io"
  gateways:
  # 需要在virtualservice中绑定Gateway
  - test-gateway/httpbin-gateway
  - mesh # applies to all the sidecars in the mesh
  http:
  - match:
    - headers:
        # 通过Header匹配，通过cookie实现定制化用户流量
        cookie:
          exact: "user=dev-123"
    route:
    - destination:
        port:
          number: 80
        host: httpbin.test-gateway.svc.cluster.local
  # 实现Http Rewrite功能
  - name: "reviews-v2-routes"
    match:
    - uri:
        prefix: "/wpcatalog"
    - uri:
        prefix: "/consumercatalog"
    rewrite:
      uri: "/newcatalog"
    route:
    - destination:
        host: reviews.dsds.svc.cluster.local
        subset: v2
  - match:
    - uri:
        prefix: /reviews/
    route:
    - destination:
        port:
          # 如果一个服务只有一个端口，可以省略port的配置
          number: 9080 # can be omitted if it's the only port for reviews
        host: reviews.dsds.svc.cluster.local
      weight: 80
    - destination:
        # 路由至不同的Namespace
        host: reviews.qa.svc.cluster.local
      weight: 20
```

# Gateway总结

网关处理的是4～6层流量

6～presentation layer  
5～session layer  
4～trasport layer  

流量最后会走到七层，通过VirtualService处理七层的流量。

# 使用Ingress来代理Gateway，但是使用istio-ingressgateway来充当controller

Kubernetes Ingress会处理4～7层流量实现路由功能；对于未注入sidecar的服务，可以实现一些路由规则，甚至可以实现灰度发布功能。但是功能较弱，如无法实现精确来控制两个版本的流量。此时可以借助istio实现更强大微服务的灰度发布功能，将这个ingress controller注入sidecar，然后借助Virtualservice来处理七层流量。如果是Http服务，甚至可以根据它的Header匹配来实现更精细化的流量控制。

但是此时有个问题：网关是否注入envoy sidecar受后端是否注入envoy sidecar的限制。能否做到网关的独立呢？

答案是可以的。

对于习惯使用了Ingress的用户，可以不用去理解Istio Gateway概念，但是使用Istion Ingressgateway功能。

默认的Nginx Ingress控制器本身是使用一个nginx提供负载均衡的功能，Ingress及其annotation来修改nginx的配置文件，如Rewrite app-root/target/Host等

使用Istio ingressgateway，其实就是将nginx换成envoy，原理不变。

同样使用Httpbin负载，删除所有的Gateway及Virtualservice，只保留httpbin deployment service。

创建Ingress，指定使用Istio：

```bash
kubectl apply -f - <<EOF
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  annotations:
    kubernetes.io/ingress.class: istio # 指定使用Istio ingressgateway
  name: http-ingress
  namespace: test-gateway
spec:
  rules:
  - host: zack6116.github.io
    http:
      paths:
      - path: /status/*  # 匹配HTTP PATH
        backend:
          serviceName: httpbin
          servicePort: 8080
EOF
```

未加Host，Ingress规则匹配不成功

```bash
[root@ssa3 ~]# curl -I  http://192.168.0.13:30084/status/200
HTTP/1.1 404 Not Found
date: Mon, 08 Feb 2021 09:44:49 GMT
server: istio-envoy
transfer-encoding: chunked


加上Host，Ingress规则生效
[root@ssa3 ~]# curl -I  http://192.168.0.13:30084/status/200 -Hhost:zack6116.github.io
HTTP/1.1 200 OK
server: istio-envoy
date: Mon, 08 Feb 2021 09:45:01 GMT
content-type: text/html; charset=utf-8
access-control-allow-origin: *
access-control-allow-credentials: true
content-length: 0
x-envoy-upstream-service-time: 26
```

# 如何使用Https来访问你的服务？

在上面的配置中有Https访问的示例，gateway需要配置tls客户端证书，但是如何生成相应的客户端/服务端证书和密钥？

## 生成自定义证书

第一步，创建根证书和私钥，来为你的服务签发证书

```bash
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -subj '/O=example Inc./CN=example.com' -keyout example.com.key -out example.com.crt
```

第二步，为你的服务创建证书和私钥

```bash
openssl req -out httpbin.example.com.csr -newkey rsa:2048 -nodes -keyout httpbin.example.com.key -subj "/CN=httpbin.example.com/O=httpbin organization"
openssl x509 -req -days 365 -CA example.com.crt -CAkey example.com.key -set_serial 0 -in httpbin.example.com.csr -out httpbin.example.com.crt
```

# 关于ingress gateway的tls端口

在官方的istio-ingressgateway的values.yaml中，有tls的配置

```bash
      # This is the port where sni routing happens
    - port: 15443
      targetPort: 15443
      name: tls
```

问题：什么是sni？tls不是一个加密算法、需配合http一起来用吗？

sni: service name indication。现在有很web server使用同一个IP，但是有很多的domain。因此在访问一个服务的时候不仅要指定IP，还要指定其domain(或者叫service name，很多时候对应Host)。否则会报ssl证书不对。

# 参考

<https://istio.io/v1.6/docs/tasks/traffic-management/ingress/ingress-control/>
<https://github.com/istio/istio/tree/master/samples/httpbin>
<https://istio.io/latest/docs/reference/config/networking/gateway/>
<https://en.wikipedia.org/wiki/OSI_model>
<https://istio.io/v1.6/docs/tasks/traffic-management/ingress/kubernetes-ingress/>
<https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/>
<https://www.cloudflare.com/learning/ssl/what-is-sni/>
