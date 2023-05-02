### ClaudeAI for Node.js

Slack Conversation Library for ClaudeAI.



### Usage

```js
import cloud from '@lafjs/cloud'

const token = 'xoxp' // slack user token
const bot = 'U05' // claude appid
const chatId = "chat-7890" // 房间名
let text = '讲个故事'

export async function main(ctx: FunctionContext) {
  const { Authenticator } = await import('claude-api-slack')
  // 相当于 ChatGPT 的新建一个回话的 回话 ID，为固定值
  const conversationId = cloud.query.conversationId
  // 通过缓存保存客户端，可以避免每次提问都是在新回话
  let claudeClient = await cloud.shared.get('claudeClient')
  if (!claudeClient) {
    claudeClient = new Authenticator(token, bot)
    await cloud.shared.set('claudeClient', claudeClient)
  }
  // 创建频道并返回房间 ID：channel，创建完房间 ID 是不会变的
  const channel = await claudeClient.newChannel(chatId)
  // 开始第一次提问
  let result = await claudeClient.sendMessage({
    text,
    channel,
    conversationId: 'ed3f4d9f-8d59-482e-8e1d-65d33ddcefb6',
    onMessage: (originalMessage) => {
      // 流式加载
      // console.log(originalMessage)
    }
  })
  console.log('==============1\n', result)
  // 开始第二次提问
  text = '接着讲，接下来进入修仙情节'
  result = await claudeClient.sendMessage({
    text, channel,
    conversationId: result.conversationId,
    onMessage: (originalMessage) => {
      console.log(originalMessage)
    }
  })
  // return { data: 'hi, laf' }
}
```

### 授权以及获取 user-token

网页 ([登录](https://app.slack.com)) 后，进入 api 配置页面 ([点我跳转](https://api.slack.com/))。

〉》点击【Create an app】

​	〉》主页看见 Your Apps 并弹出窗口【Create an app】  〉》点击【From scratch】

​	〉》填写 app 名称以及选择工作空间（例：name: Bot, workspace: chat）	 〉》点击【Create App】

​	〉》点击左侧边栏上的【OAuth & Permissions】	 〉》下拉至【Scopes】卡片，在【User Token Scopes】项下添加权限，如下：

​							channels:history,  channels:read,  channels:write,  groups:history,  groups:read,  groups:write, 

​							chat:write,  im:history,  im:write,  mpim:history,  mpim:write

​	〉》回到顶部【OAuth Tokens for Your Workspace】栏，点击【Install to Workspace】，然后确认授权即可


至此，获得拥有一定权限的 user-token

<img src="static/截屏2023-04-18 09.10.56.png" alt="截屏2023-04-18 09.10.56" style="zoom:50%;" />


<img src="static/截屏2023-04-18 09.14.41.png" alt="截屏2023-04-18 09.14.41" style="zoom:50%;" />


### 获取 claude appid

<img src="static/截屏2023-04-18 08.49.20.png" alt="截屏2023-04-18 08.49.20" style="zoom:50%;" />

Credits
Thank you to:

- https://github.com/ikechan8370/chatgpt-plugin original NodeJS implementation
