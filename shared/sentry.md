# [sentry](https://docs.sentry.io/)分享
> 应用监控平台。

* [fundebug](https://www.fundebug.com/)

## 部署
### 必要条件（以最新sentry为例，旧版本sentry部署过程略有不同）
* docker 17.05.0+
* docker-compose 1.23.0+
* python (建议3.0+)
* git
* 2400MB RAM

### 过程
> 安装注意事项git [README](https://github.com/getsentry/onpremise) 简要介绍。docker、pip等需要配置下国内源，不然初始化镜像的时候非常慢
1. 进入任意安装目录，拉取sentry 安装git。`git clone https://github.com/getsentry/onpremise.git`
2. 执行安装脚本。`sh ./install.sh`
3. 运行docker。`docker-compose up -d`

## 配置
> 配置文件：config.yml、sentry.conf.py、.env

### 邮件配置
* config.yml 文件配置邮件服务
```
  # mail.backend: 'smtp'  # Use dummy if you want to disable email entirely
  mail.host: 'smtp.mxhichina.com' # 发送邮件smtp服务
  mail.port: 80 # 发送邮件smtp服务端口
  mail.username: 'tg.ding@js-datacraft.com' # 发送邮件smtp服务账号
  mail.password: 'Dean1234' # 发送邮件smtp服务密码
  mail.use-tls: true # 发送邮件smtp服务是否使用ssl服务
  # The email address to send on behalf of
  mail.from: 'tg.ding@js-datacraft.com'
```

### 创建超级用户
`docker-compose run --rm web createuser --email admin888@example.com --password admin --superuser`

## 使用
* 初始化及手动上报文件
```
  import Vue from 'vue'
  import * as Sentry from '@sentry/browser'
  import { Vue as VueIntegration } from '@sentry/integrations'

  const dsn = 'http://a089e9c7752c4b94a87c6dd1ec0d0a08@10.3.6.19:9000/2'

  // main.js中初始化
  Sentry.init({
    dsn,
    integrations: [new VueIntegration({ Vue, attachProps: true })]
  })

  // 手动上报：实例化Sentry
  const client = new Sentry.BrowserClient({
    dsn
  })
  const hub = new Sentry.Hub(client)
  hub.captureException(error)

```

* 上报设置
```
  // 配置额外上报信息，如请求url及参数等
  hub.setExtra('config', config)
  // 配置额外标签信息等
  hub.addTag("tagName", "tagValue");
  // 配置额外的用户信息
  hub.setUser('user', 'userInfo')
  // 配置额外的错误等级
  hub.setLevel('level', 'level')

  // ...
```

## 原理浅析
### 错误监听
* Sentry对[window.onerror](https://developer.mozilla.org/zh-CN/docs/Web/API/GlobalEventHandlers/onerror)函数进行了改写，在这里实现了错误监控的逻辑，添加了很多运行时信息帮助进行错误定位。
* 在我们使用Promise的时候，如果发生错误而我们没有去catch的话，window.onerror是不能监控到这个错误的。JavaScript引擎会触发unhandledrejection事件，只要我们监听这个事件，那么就能够监控到Promise产生的错误。

### 错误处理
* 如果接收到的是一个ErrorEvent对象，那么直接取出它的error属性即可，这就是对应的error对象。
* 如果接收到的是一个DOMError或者DOMException，那么直接解析出name和message即可，因为这类错误通常是使用了已经废弃的DOMAPI导致的，并不会附带上错误堆栈信息。
* 如果接收到的是一个标准的错误对象，不做处理
* 如果接收到的是一个普通的JavaScript对象，Sentry会将这个对象的Key序列化为字符串，然后会触发报错 'Non-Error exception captured with keys: ' + serializeKeysForMessage(exKeys)

## 参考
* [浅谈Sentry前端监控原理](https://zhuanlan.zhihu.com/p/75577689)
* [Fundebug一步一步搭建前端监控系统：JS错误监控篇](https://zhuanlan.zhihu.com/p/72451502)
* [把前端监控做到极致](https://zhuanlan.zhihu.com/p/32262716)

