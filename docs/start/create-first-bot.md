# 创建你的第一个 Bot

本章将会以一个简单的 QQ 机器人为例，介绍最基本的 Bot 搭建过程，来带领你初步认识 Flandre。

## 设置环境

Flandre 最低需要 .NET 6 环境，使用前请确保自己的 .NET 版本没有过低。

使用你喜欢的 IDE 或编辑器，创建一个新的控制台项目，并添加 NuGet 包引用。

::: tip
首先添加 `Flandre.Framework` 包，这是搭建一个 Flandre 应用所需要的基本框架。另外，为了使我们的 Bot 能够成功在平台上正常工作，你需要添加一个适配器。这里使用 `Flandre.Adapters.Konata`（QQ 协议的 .NET 实现，基于 [Konata.Core](https://github.com/KonataDev/Konata.Core)）作为演示。
:::

## 创建应用

打开 `Program.cs`，写下以下代码：

```csharp
using Flandre.Framework;
using Flandre.Framework.Common;
using Flandre.Adapters.Konata;
using Konata.Core.Common;

var builder = new FlandreAppBuilder();

// 添加 Bot 配置
var config = new KonataAdapterConfig();
config.Bots.Add(new KonataBotConfig
{
    KeyStore = new BotKeyStore("<QQ 号>", "<密码>")
});

var app = builder
    .UseAdapter(new KonataAdapter(config))
    .UsePlugin<ExamplePlugin>()
    .Build();

app.Run();

class ExamplePlugin : Plugin
{
    public override async Task OnMessageReceived(MessageContext ctx)
        => await ctx.Bot.SendMessage(ctx.Message);
}
```

以上内容构成了一个最简单的复读小程序。

## 分析我们的程序

首先引入命名空间，不必多说。其次，我们创建了一个新的 `FlandreAppBuilder` 实例，是我们应用的基本构造器。在一个 `FlandreAppBuilder` 实例里，我们可以添加插件、适配器等一系列基础服务。

```csharp
var builder = new FlandreAppBuilder();
```

我们接着创建了 `Konata` 适配器的配置对象，并将 Bot 信息添加到其中。

```csharp
var config = new KonataAdapterConfig();
config.Bots.Add(new KonataBotConfig
{
    KeyStore = new BotKeyStore("<QQ 号>", "<密码>")
});
```

::: info
由于 C# 的顶层语句限制，我们只能在文件最后定义类。这里由于逻辑优先关系，我们先讲解这个类。
:::

我们在文件末端创建了一个新的插件类，并继承了 `Plugin` 基类。在其中，我们重写了基类的 `OnMessageReceived` 方法，接受消息并调用 Bot 的发送消息接口，原路发送回去。每当 Bot 收到消息时，该方法就会被调用。

```csharp
class ExamplePlugin : Plugin
{
    public override void OnMessageReceived(MessageContext ctx)
        => ctx.Bot.SendMessage(ctx.Message);
}
```

然后，在 `builder` 实例上注册适配器和我们自定义的插件，并构造为实际的 `FlandreApp` 实例：

```csharp
var app = builder
    .UseAdapter(new KonataAdapter(config))
    .UsePlugin<ExamplePlugin>()
    .Build();
```

最后，启动应用：

```csharp
app.Run();
```

将 Bot 的 QQ 号、密码填入相应空位，运行程序，可能会提示登录验证。按照要求登陆成功后，向 Bot 发送一条消息：

- <Badge text="发送" /> Hello, Flandre!
- <Badge text="接收" /> Hello, Flandre!

::: tip
至此你已经完成了第一个小 Bot 的开发！接下来的一节内容中，你将了解到 Flandre 的结构组成，以及各种实体模型的简单介绍。
:::
