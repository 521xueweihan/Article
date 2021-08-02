# Rubick 从萌芽到开源的心路历程
前几个月在 github 上开源了一款基于 `electron` 实现的媲美 `uTools` 的工具箱：[Rubick](https://github.com/clouDr-f2e/rubick) 。出发点是为了解决公司内部插件无法发布到 `uTools` 生态但又需要类似 `uTools` 那样的插件化能力。同时开源社区也希望了解当前社区内是否也有这样的诉求，或许可以帮助我们更好的完善和改进。在开源的过程中还是遇到了不少问题和挑战，谨以此文记录与 `Rubick` 有关的我的心路历程。

## 项目体验安装
github 项目地址：[Rubick](https://github.com/clouDr-f2e/rubick)

工具下载链接：[下载体验](https://github.com/clouDr-f2e/rubick#%E5%AE%89%E8%A3%85%E5%8C%85)

![demo](https://user-images.githubusercontent.com/21073039/124477360-8770f980-ddd6-11eb-8dc3-ba318223697f.gif)

## 项目孵化
### 启蒙
最开始接触 `electron` 是因为看到了 `PicGo` 作者在掘金上推广他的图床工具。他的一个核心功能非常吸引我，就是 `macos` 下可以直接拖拽图片进入任务托盘上传图片：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6052407ab720424ebfacb4029a8a4570~tplv-k3u1fbpfcp-zoom-1.image)

当时正好我们团队也需要搞一个内部的 cdn 图片资源管理图床，用于项目图片资源压缩并直接上传到 cdn 上，之前我们做了个网页版。而这里我深刻的感受到了 `electron` 的强大，可以极大的提高工作效率，参考 `PicGo`，我尝试做了第一个 `electron` 项目，完成了基于 `tinypng` 的图片压缩上传到内部 cdn 的桌面端应用 [tinypng-upload](https://github.com/muwoo/tinypng-upload) 。

### 发展
之后公司内部因为开发需要，需要和后端进行接口联调，测试环境的时候，经常会涉及到一些状态改变要看交互样式的问题。比如测试需要测商品的待支付、支付中、支付完成等各种节点的交互样式是否符合预期，这种情况测试一般会去造数据或者让后端改数据库接口。有的小伙伴可能会用 `Charles` 修改返回数据进行测试。但是 `Charles` 的抓包体验和配置体验感觉有点麻烦，对新人不是很友好，所以我们自己做了个非常易用 `抓包&mock` 工具：

![抓包](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fbe19cfe19ff4fb099202c7a87addc7f%7Etplv-k3u1fbpfcp-watermark.image)

这也是 `Rubick` 最早的雏形。

随后，我们发现，当页面发布线上的时候，没有办法在微信环境内对线上页面进行调试，所以开发了一个基于 `winner` 的远程调试功能：

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5add0779932746da9364ca754759f829~tplv-k3u1fbpfcp-watermark.image)

随着 `Rubick` 在内部不断推广和使用，所需功能也越来越多。我们需要 `需求管理`、`一键VPN`、`性能评估`、`埋点检测` 等等工具。这些工具的增加一方面导致我们的 `Rubick` 体积暴增，一方面又导致了用户需要不断更新软件，导致用户体验非常差。

其次，我们在推广给测试、UI 同学使用的时候，发现他们其实并不关注前面的页面调试、性能测评等功能，可能只是用到其中某一项，所以整个项目对他们来说就显得很臃肿。直到有一天，我在掘金上看到这样一个沸点：


![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/966854586d0a47e7aa17e751058ba275~tplv-k3u1fbpfcp-watermark.image)

下面有个评了让我产生了兴趣和灵感：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a61b1dabcf6d4a2d85002967d37de92a~tplv-k3u1fbpfcp-watermark.image)

所以这是我第一次和 `uTools` 产生了交集，在体验 `uTools` 功能后，我长吸一口气：这不就是我想要的嘛！然后就去 `github` 上找 `uTools` 发现并没有开源，所以要想发布我的功能到 `uTools` 上目前来看只能发布到公网。但这又涉及到数据安全的问题。无奈，难道真的要自己做一个这样的工具吗？真的是有点头大。不过想想如果真的能给团队提效，带来价值还是挺值得去尝试的。至此，我萌生了要开发一个媲美 `uTools` 的开源工具箱的念头。

## 项目研发
开篇第一步，按照我之前的套路都是先取好名字先占个坑，之前写了一本《从0开始可视化搭建》的小册，里面基于 dota 取了个 `coco` 的名字。这次我取名的是 `rubick` 即 `拉比克`。Rubick(拉比克) 是 dota 里面的英雄之一，其核心技能是插件化使用其他英雄的技能，用完即走。非常符合本工具的设计理念，所以取名 `Rubick`。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/33a4e360a4044315a719910131f56bd2~tplv-k3u1fbpfcp-watermark.image)

### 支持插件化
我们的核心目标就是需要让 `Rubick` 支持插件化，解决前面提到的问题，其次，通过调研了解到团队内有些同学已经在使用 `uTools` 了，要想让他们从 `uTools` 上把插件 0 成本迁移到 `Rubick`上，就必须实现 `uTools` 的部分 API 能力，以及插件的定义和写法也需要和 `uTools` 规范保持一致。

另外插件开发需要和 `rubick` 进行联调，所以 `rubick` 需要支持开发者模式，帮助开发者更好的开发插件。首先我们先建一个 `plugin.json` 用于描述插件的基础信息：

```json
{
  "pluginName": "测试插件",
  "author": "muwoo",
  "description": "我的第一个 rubick 插件",
  "main": "index.html",
  "version": "0.0.2",
  "logo": "logo.png",
  "name": "rubick-plugin-demo",
  "gitUrl": "",
  "features": [
    {
      "code": "hello",
      "explain": "这是一个测试的插件",
      "cmds":["hello222", "你好"]
    }
  ],
  "preload": "preload.js"
}
```
##### 核心字段说明
* `name` 插件仓库名称，用于 github dowload 标致
* `pluginName` : 插件名称。
* `description` : 插件描述，简洁的说明这个插件的作用
* `main` : 入口文件，如果没有定义入口文件，此插件将变成一个模版插件
* `version` : 插件的版本，需要符合 Semver (语义化版本) 规范。用于版本更新提示
* `features` : 插件核心功能列表
* `features.code` : 插件某个功能的识别码，在进入插件时会传递给你的代码，可用于区分不同的功能，显示不同的 UI
* `features.cmds` : 通过哪些方式可以进入这个功能，中文会自动支持 拼音及拼音首字母，无须重复添加

开发插件的方式是复制 `plugin.json` 进入到 `rubick` 的搜索框，所以需要监听搜索框的`change` 事件，用于读取当前剪切板复制的内容：
```js
onSearch ({ commit }, paylpad) {
  // 获取剪切板复制的文件路径
  const fileUrl = clipboard.read('public.file-url').replace('file://', '');
  
  // 如果是复制 plugin.json 文件
  if (fileUrl && value === 'plugin.json') {
     // 读取 json 文件
     const config = JSON.parse(fs.readFileSync(fileUrl, 'utf-8'));
     // 生成插件配置
     const pluginConfig = {
        ...config,
        // 记录 index.html 存方的路径
        sourceFile: path.join(fileUrl, `../${config.main || 'index.html'}`),
        id: uuidv4(),
        // 标记为开发者
        type: 'dev',
        // 读取 icon
        icon: 'image://' + path.join(fileUrl, `../${config.logo}`),
        // 标记是否是模板
        subType: (() => {
          if (config.main) {
            return ''
          }
          return 'template';
        })()
      };
  }
}
```
到这里我们已经可以根据复制的 `plugin.json` 能获取到插件的最基础的信息，接下来就是需要展示搜索框：

```js
 commit('commonUpdate', {
    options: [
      {
        name: '新建rubick开发插件',
        value: 'new-plugin',
        icon: 'https://static.91jkys.com/activity/img/b37ff555c748489f88f3adac15b76f18.png',
        desc: '新建rubick开发插件',
        click: (router) => {
          commit('commonUpdate', {
            showMain: true,
            selected: {
              key: 'plugin',
              name: '新建rubick开发插件'
            },
            current: ['dev'],
          });
          ipcRenderer.send('changeWindowSize-rubick', {
            height: getWindowHeight(),
          });
          router.push('/home/dev')
        }
      },
      {
        name: '复制路径',
        desc: '复制路径',
        value: 'copy-path',
        icon: 'https://static.91jkys.com/activity/img/ac0d4df0247345b9a84c8cd7ea3dd696.png',
        click: () => {
          clipboard.writeText(fileUrl);
          commit('commonUpdate', {
            showMain: false,
            selected: null,
            options: [],
          });
          ipcRenderer.send('changeWindowSize-rubick', {
            height: getWindowHeight([]),
          });
          remote.Notification('Rubick 通知', { body: '复制成功' });
        }
      }
    ]
});

```
到这里，当复制 `plugin.json` 进入搜索框时，变可直接出现 2个选项，一个新建插件，一个复制路径的功能：


![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9a22b242ba7046e790f45595bd255d4e~tplv-k3u1fbpfcp-watermark.image)

当点击`新建 rubick 插件` 功能时，则需要跳转到 `home` 页，加载插件的基础类容，唯一需要注意的是 `home` 页加载的内容高度应该是rubick最大窗口的高度。所以需要调整窗口大小：
```js
 ipcRenderer.send('changeWindowSize-rubick', {
    height: getWindowHeight(),
 });
```
关于 `renderer` 里面的 vue 代码这里就不再详细介绍了，因为大多是 css 画一下就好了，直接来看展示界面：


![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ff867c84a2e641329390c80a12ab59a4~tplv-k3u1fbpfcp-watermark.image)

到这里，就完成了开发者模式，接下来再介绍如何让插件在 `rubick` 中跑起来。

### 运行插件
运行插件需要容器，electron 提供了一个 `webview` 的容器来加载外部网页。所以我们可以借助 `webview` 的能力实现动态网页渲染，这里所谓的网页就是插件。但是网页无法使用`node`的能力，而且我们做插件的目的就是为了开放与约束，需要对插件开放一些内置的 `API` 能力。好在 `webview` 提供了一个 `preload` 的能力，可以在页面加载的时候去预置一个脚本来执行。

也就是说我们可以给自己的插件写一个 `preload.js` 来加载。但这里需要注意我们既要保持插件的个性又得向插件内注入全局 `API` 供插件使用，所以可以直接加载 `rubick` 内置 `preload.js`，`preload.js` 内再加载个性化的 `preload.js`:

```html
// webview plugin.vue
<webview id="webview" :src="path" :preload="preload"></webview>
<script>
export default {
  name: "index.vue",
  data() {
    return {
      path: `File://${this.$route.query.sourceFile}`,
      // 加载当前 static 目录中的 preload.js
      preload: `File://${path.join(__static, './preload.js')}`,
      webview: null,
      query: this.$route.query,
      config: {},
    }
  }
}
</script>
```

对于 `preload.js` 我们就可以这么用啦：
```js
if (location.href.indexOf('targetFile') > -1) {
  filePath = decodeURIComponent(getQueryVariable('targetFile'));
} else {
  filePath = location.pathname.replace('file://', '');
}


window.utools = {
  // utools 所有的 api 实现
}
// 加载插件 preload.js
require(path.join(filePath, '../preload.js'));
```

到这里就已经实现了一个最基础的插件加载，我们来看看效果：

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/33b05667411441b7b8a65b58dc20f6f7~tplv-k3u1fbpfcp-watermark.image)

### 支持更多体验能力
随后为了更加贴近 `uTools` 的体验，我们又开始着手让 `Rubick` 支持更多原生体验增强的特性：
#### 超级面板
长按鼠标右键，即可呼起超级面板，可以根据当前鼠标选择内容，匹配对应插件能力。比如当前选择图片后长按右击，则会呼起上传图床插件：

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1706cc730f1f46078cb700a445211317~tplv-k3u1fbpfcp-watermark.image)

#### 模板
为了更贴合 `uTools` 的插件能力，需要实现模板功能，模板即是一个内置 UI 样式的功能插件。

<img src=https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0b113ad547974699b9c73c28bc09b9b1~tplv-k3u1fbpfcp-watermark.image width=500 />

#### 系统命令
##### 取色
基于 `robot.js` 以及 `iohook` 实现。未使用 C++ 扩展。输入框内输入 `取色` 或者 `colorpocker` 唤起

![image](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3036ae85bf3549fc8bbbe2926ecbad55~tplv-k3u1fbpfcp-watermark.image)

##### 截屏
输入框内输入 `shortcut` 或者 `截屏` 唤起

<img src=https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/18023dab52e1420c9e87362cefddb2a1~tplv-k3u1fbpfcp-watermark.image width=500 />


#### 全局快捷键

<img src=https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/62cc424eacac4c9eb178f0e055e87d9a~tplv-k3u1fbpfcp-watermark.image width=500 />

## 推广
既然开源社区了，所以我们也希望有更多的社区小伙伴可以看到这个项目，让项目得到更多地意见和反馈，从而帮助 `Rubick` 成长。于是我在知乎、cnode、v2ex 还有掘金都发了文章。开源当天就收货到了 100+ star，其实内心还是挺激动的，至少知道其实外部可能也是有这样的痛点在的。但随后我也收到了社区的一些质疑：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2ebe95d6c26b4fe49c118acaa9673ad6~tplv-k3u1fbpfcp-watermark.image)

其实这个时候我内心真的是很委屈的，我的初衷只是为了做技术分享，类似于一些《实现京东商城》、《实现简版Vue》、《实现淘宝商城》等作者的初衷一样，希望可以帮助有需要的人和社区有一些交集罢了。那个时候我有一段时间觉得做这个东西可能真的不讨好，准备放弃了。但当我收到这个评论时，觉得自己做的还是挺有意义的：


![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7944744e1410451bb3206c038ed91532~tplv-k3u1fbpfcp-watermark.image)

随后机缘巧合我看到了 `helloGithub` 这样一个开源项目推荐的平台，了解了他的作者的事迹，感觉也是被他的故事吸引。作者本身也是喜欢打 dota。后来学的 `python` 接触的 `github`。发现了很多很多优秀的开源项目可以学习，因此也诞生了 `helloGithub`。所以啊，开源的魅力不正是如此吗？技术分享，能力成长，帮助有需要的同学，一起进步和成长。

后面我也就投稿了 `helloGithub` 也非常感谢作者提供的平台。

## 最后
最后还是要致谢一下 `uTools`。另外重申一下 `Rubick` 的立场：我们旨在技术分享，如果大家有需要开发一款类似的工具箱，提供一种技术方案和思路，并不是商业化的目的。`uTools` 非常优秀，也强烈建议大家去使用 `uTools`。如果 `Rubick` 对您有帮助，也帮忙给个 star ✨ 鼓励一下：[https://github.com/clouDr-f2e/rubick](https://github.com/clouDr-f2e/rubick)

