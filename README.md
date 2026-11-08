# PCL 插件模板

> 这是一个可以直接使用的 Plain Craft Launcher 插件模板仓库。
> 点右上角 **Use this template** 创建自己的插件，改完 `git push` 就能被插件广场搜到。

## 广场只收录基于本模板的仓库（白名单）

插件广场**不再**收录「随便打了个 topic」的仓库。要进广场，仓库里必须有本模板自带的标记文件：

```
.pcl-plugin.json
```

内容里这一行是关键，**不要改**：

```json
"template": "1740443398/pcl-plugin-template"
```

启动器扫描时的判断顺序：

1. 用 GitHub 搜索发现打了 `pcl-plugin` topic 的仓库（只是候选）
2. 逐个拉取候选仓库的 `.pcl-plugin.json`
3. `template` 字段等于 `1740443398/pcl-plugin-template` 才收录，否则静默跳过

所以：

- **不要删除 `.pcl-plugin.json`** —— 删了广场就不再收录，你只会看到「没有 .pcl-plugin.json」的跳过记录
- **不要改 `template` 字段** —— 改成别的值等于声明「我不是这个模板的插件」，同样会被跳过
- 用 **Use this template** 或者 **Fork** 都可以，两种方式都会把这个文件带过去

为什么用标记文件而不是查 GitHub 的 fork 关系：用 Use this template 建出来的仓库**不是 fork**，API 里 `parent` 是空的，只看 fork 关系会漏掉最主流的用法。而文件内容两种方式都会保留。

> 这只是收录门槛，不是安全背书。装的时候启动器仍会做一次静态审计，含危险内容的会被拒绝并删除。

## 这个模板能做什么

装上之后，你的插件会像内置功能一样出现在启动器里：

- 标题栏多一个入口（`titlebar: true`）
- 点进去是你自己的界面
- 有独立的设置页
- 可以读设置、弹提示、发网络请求

## 三步开始

### 1. 创建仓库

点右上角 **Use this template** → **Create a new repository**，名字随便起。

或者手动：

```bash
git clone https://github.com/1740443398/pcl-plugin-template.git my-plugin
cd my-plugin
```

### 2. 改清单

编辑 `plugin.json`，**必须改这三项**：

```json
{
  "id": "com.yourname.myplugin",   // 改成你自己的，不要和别人的撞
  "name": "我的插件",               // 显示名
  "version": "1.0.0"                // 版本号
}
```

### 3. 发布

```bash
git add .
git commit -m "我的第一个插件"
git push
```

然后到 GitHub 仓库首页 → 右侧 **About** 旁的齿轮 → **Topics** 里加：

```
pcl-plugin
```

加完等几分钟，启动器的「插件广场」就能搜到你（前提是 `.pcl-plugin.json` 还在，且 `template` 字段没被改）。

## 目录说明

```
.
├── .pcl-plugin.json     # 广场白名单标记（必须保留，不要改 template 字段）
├── plugin.json          # 清单（必须）
├── main.xaml            # 主界面（必须）
├── config.xaml          # 设置页（可选）
├── icon.png             # 头像（可选，留空自动用首字母）
└── README.md            # 这个文件
```

## main.xaml 怎么写

最简：

```xml
<local:MyCard Title="我的插件">
    <StackPanel Margin="25,38,25,15">
        <TextBlock Text="Hello, PCL!" FontSize="15" />
    </StackPanel>
</local:MyCard>
```

**三条必须遵守的规则**：

1. **颜色用主题变量**，不要写死色值

   ```xml
   <!-- 对 -->
   <TextBlock Foreground="{DynamicResource ColorBrush1}" Text="正文" />
   <!-- 错：深色主题下会变成刺眼白块 -->
   <TextBlock Foreground="White" Text="正文" />
   ```

2. **文本必须换行**

   ```xml
   <TextBlock TextWrapping="Wrap" Text="很长很长的说明……" />
   ```

3. **不要写死高度**，让内容自己撑开

## 能用的控件

| 控件 | 说明 |
|---|---|
| `local:MyCard` | 卡片，`Title` 属性设标题 |
| `local:MyButton` | 按钮 |
| `local:MyTextBox` | 输入框 |
| `local:MyComboBox` | 下拉框 |
| `local:MyCheckBox` | 复选框 |
| `local:MyListItem` | 列表项 |
| `local:MyHint` | 提示条，`Theme` 可设 Yellow/Red/Green |
| `TextBlock` | 文本 |
| `Grid` / `StackPanel` | 布局 |

## 主题颜色变量

| 变量 | 用途 |
|---|---|
| `ColorBrush1` | 正文 |
| `ColorBrush2` | 次要文字 |
| `ColorBrush3` | 强调与标题 |
| `ColorBrushGray1` | 浅背景 |
| `ColorBrushGray3` | 小字说明 |

## 常见问题

**插件不显示？**
- 检查目录结构：必须是 `PCL/Plugins/你的文件夹/plugin.json`
- `id` / `name` / `version` 三个必填
- 回插件页点「重新扫描」

**改了文件没生效？**
- 点「重新扫描」，不用重启启动器

**想看日志？**
- `PCL/Log1.txt`，会写明哪个文件哪一行出错

**广场里搜不到我的插件？**
- 仓库里缺 `.pcl-plugin.json`，或 `template` 字段被改了
- 没给仓库加 `pcl-plugin` topic
- 仓库是私有的
- 日志里搜「插件广场跳过」，会写明具体是哪个仓库、为什么跳过

## 安全边界（重要）

插件**不能**：

- 启动外部程序
- 读写插件目录以外的文件
- 读账号凭据
- 改游戏文件

这是故意的。插件能做什么，取决于启动器提供哪些接口，而不是它能 import 什么。

安装前启动器会静态审计，含危险内容的会被**拒绝并删除**。

## 完整文档

见 [PCL 插件开发文档](https://github.com/1740443398/Plain-Craft-Launcher-by-Slate_xd/blob/main/docs/PLUGIN.md)

## 许可

这个模板本身是 MIT，随便用。
