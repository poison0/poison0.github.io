### 开发环境搭建
IDEA 分为两个版本：
社区版（Community）：完全免费，代码开源，但是缺少一些旗舰版中的高级特性
旗舰版（Ultimate）：30天免费，支持全部功能，代码不开源
Plugin DevKit 是 IntelliJ 的一个插件，它使用 IntelliJ IDEA 自己的构建系统来为开发 IDEA 插件提供支持。开发 IDEA 插件之前需要安装并启用 Plugin DevKit 。
打开 IDEA，导航到 Settings | Plugins，若插件列表中没有 Plugin DevKit，点击 Install JetBrains plugin，搜索并安装。

官方文档地址(https://plugins.jetbrains.com/docs/intellij/welcome.html?from=jetbrains.org) 
###开发一个简单插件

#### action
* Action ID: action 唯一 id，推荐 format: PluginName.ID
* Class Name: 要被创建的 action class 名称
* Name: menu item 的文本
* Description: action 描述，toolbar 上按钮的提示文本，可选
* Add to Group：选择新 action 要被添加到的 action group（Groups, Actions）以及相对其他 actions 的位置（Anchor）
* Keyboard Shortcuts：指定 action 的第一和第二快捷键

```java
    Project project = event.getData(PlatformDataKeys.PROJECT);
    String txt= Messages.showInputDialog(project, "What is your name?", "Input your name", Messages.getQuestionIcon());
    Messages.showMessageDialog(project, "Hello, " + txt + "!\n I am glad to see you.", "Information", Messages.getInformationIcon());
```
#### 窗口
窗口需要听见扩展，扩展代码如下  
pluge.xml
```xml
  <extensions defaultExtensionNs="com.intellij">
    <!-- Add your extensions here -->
    <toolWindow factoryClass="chandao.action.ListWindowFactory" id="禅道" anchor="bottom"></toolWindow>
  </extensions>
```
创建窗口的类需要继承 ToolWindowFactory 接口并实现 createToolWindowContent 核心代码如下

```java
    ContentFactory instance = ContentFactory.SERVICE.getInstance();
    Content content = instance.createContent(panel, "", false);
    toolWindow.getContentManager().addContent(content);
```
窗口编辑类似java swing Gui

#### 工具栏
创建工具栏之前需要创建一个 SimpleToolWindowPanel 对象，实现如下
```java
public class ToolWindowPanel extends SimpleToolWindowPanel {

    public ToolWindowPanel() {
        super(false,true);
    }
}
```
可以添加工具栏和内容
```java
    panel.setToolbar(createToolBar().getComponent());
    panel.setContent(contentPanel);
```
#### 数据持久化

获取值
```java
    PropertiesComponent instance = PropertiesComponent.getInstance();
    String userName = instance.getValue("chandao_user_name");
    String passWord = instance.getValue("chandao_pass_word");
```
存储值
```java
    PropertiesComponent instance = PropertiesComponent.getInstance();
    instance.setValue("chandao_pass_word",password);
    instance.setValue("chandao_user_name",user);
```

#### psi对象
https://blog.csdn.net/excellentyuxiao/article/details/80273448

有用的博客和视频  
https://www.cnblogs.com/kancy/p/10654569.html  
https://www.bilibili.com/video/BV1Zi4y1b7fw/?spm_id_from=333.788.recommend_more_video.-1
https://plugins.jetbrains.com/plugin/10650-json-parser
