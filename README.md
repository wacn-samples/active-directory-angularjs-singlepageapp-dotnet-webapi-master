---
services: active-directory
platforms: javascript
author: dstrockis
---


#如何使用 Azure AD 保护 AngularJS 单页面应用程序

====================

这个示例演示如何使用JavaScript的ADAL库来保护单页面应用程序，后台使用ASP.NET Web API来实现，它调用另一个采用CORS的ASP.NET Web API。

ADAL Javascript是一个开源的库。关于分发选项、源代码以及贡献，请访问[ADAL的资源库](https://github.com/AzureAD/azure-activedirectory-library-for-js)。

有关这个协议在这个场景及其他场景下如何工作的更多详细信息，请阅读：[Azure AD 的身份验证方案](https://www.azure.cn/documentation/articles/active-directory-authentication-scenarios/)。

## 如何运行这个示例

- Visual Studio 2013
- 连接互联网
- 一个Azure的订阅（如果您还没有Azure订阅，请点击[此处](https://www.azure.cn/)申请试用的订阅账号）。

每一个Azure订阅都有一个相关联的Azure Active Directory租户。如果你还没有Azure订阅，你可以在[https://www.azure.cn/](https://www.azure.cn/)注册一个免费的订阅。这个示例使用到的Azure AD的所有功能都是免费的。


### 第一步： 克隆或者下载这个资源库

从你的shell或者命令行：

	git clone https://github.com/wacn-samples/active-directory-angularjs-singlepageapp-dotnet-webapi.git

### 第二步：在您的Azure AD租户注册To Go API程序


1. 登录[门户网站](https://manage.windowsazure.cn)。
2. 点击左侧导航栏中的Active Directory 。
3. 点击你想注册示例程序的目录租户。
4. 点击程序标签。
5. 点击下方的“添加”。
6. 选择“Web 应用程序和/或 Web API”。
7. 为您的程序输入友好的名字，例如示例“To Go API”，然后点击继续。
8. 在登录URL中输入示例的基地址，默认是`https://localhost:44327/`。
9. 在应用程序 ID URI中输入`https://<your_tenant_name>/ToGoAPI`，使用你的Azure AD租户的名字替代`<your_tenant_name>`

都完成了！在进入下一个步骤前，你需要找到程序的应用程序 ID URI。

1. 如果然在Azure门户，点击你程序的配置标签。
2. 找到应用程序 ID URI的值，然后复制到粘贴板。

### 第三步：在您的Azure AD租户中配置To Go API程序

1. 在Visual Studio 2013中打开解决方案。
2. 在ToGoAPI项目中打开`web.config`文件。
3. 找到程序键值 `ida:Tenant`，然后使用你的AAD租户的名字替换掉它的值。
4. 找到程序键值`ida:Audience`，然后使用从Azure管理门户复制的应用程序 ID URI替换掉它的值。
5. 找到程序键值`ida:MetadataAddress`，然后再Azure管理门户中点击下方的“查看端点”，复制联合元数据文档的值替换掉它的值。
6. 在ToGoAPI项目中，打开文件`Controllers/ToGoListController.cs`，在`[EnableCors...]`特性中输入To Do SPA的客户端程序，默认值是`https://localhost:44326`.确保省去值最后面的斜杠。
7. 在TodoSPA项目中，打开文件`App/Scripts/App.js`然后定位到`endpoints`对象。
8. 输入的映射To Go API端点到它的资源标识符。`endpoints`对象的属性应该是To Go API的位置。默认是`https://localhost:44327/`。这个属性的值是从Azure门户复制的应用程序 ID URI，例如：`https://<your_tenant_name>/ToGoAPI`。
9. 不要担心这个文件中其他的配置值，我们稍后会介绍。
10. 在TodoSPA项目中打开`App/Scripts/toGoListSvc.js`文件，然后使用To Go API地址的值来替换掉`apiEndpoint`变量的值。默认是：`https://localhost:44327/`。

### 第四步：在您的Azure AD租户中注册单页面应用程序

1. 再次登录[门户网站](https://manage.windowsazure.cn)。
2. 点击左侧导航栏中的Active Directory 。
3. 点击你想注册示例程序的目录租户。
4. 点击程序标签。
5. 点击下方的“添加”。
6. 选择“Web 应用程序和/或 Web API”。
7. 为您的程序输入友好的名字，例如示例“To Do SPA”，然后点击继续。
8. 在登录URL中输入示例的基地址，默认是`https://localhost:44326/`。
9. 在应用程序 ID URI中输入`https://<your_tenant_name>/ToDoSPA`，使用你的Azure AD租户的名字替代`<your_tenant_name>`
10. 在”针对其他应用程序的权限“节点下，点击“添加应用程序”。选择“所有应用”，找到To Go API应用程序，然后单击它。之后点击下方的“完成”按钮。在“委托的权限”中选择"Access To Go API"，然后保存配置。

都完成了！在进入下一个步骤前，你需要找到程序的客户端ID。

1. 如果然在Azure门户，点击你程序的配置标签。
2. 找到客户端ID的值，然后复制到粘贴板。

### 第五步：为您的应用程序开启OAuth2隐式流

默认情况下，应用程序的Azure AD的应用程序中未启用OAuth2隐式流。为例运行这个示例，您需要明确的启用它。

1. 在之前的步骤中，您的浏览器应该还在Azure管理门户页面 - 应该还显示着您程序的配置页。
2. 使用下方的“管理清单”按钮，下载程序的清单，然后保存在本地。
3. 使用文本编辑工具打开清单文件，找到 `oauth2AllowImplicitFlow`属性，您将发现它的值是`false`，将它变成`true`然后保存文件。
4. 使用“管理清单”按钮，上传更新过的清单文件，保存程序的配置。

### 第六步：配置To Do SPA已使用您的Azure AD租户

1. 在Visual Studio 2013中打开解决方案。
2. 在TodoSPA项目中，打开`web.config`文件。
3. 找到程序的键值`ida:Tenant`，然后使用AAD租户的名字替换掉它的值。
4. 找到程序的键值`ida:Audience`然后使用Azure门户网站中的客户端ID替换掉它的值。
5. 找到程序键值`ida:MetadataAddress`，然后再Azure管理门户中点击下方的“查看端点”，复制联合元数据文档的值替换掉它的值。
5. 在TodoSPA项目中，打开文件`App/Scripts/App.js`，定位到`adalAuthenticationServiceProvider.init(`。
6. 使用您的AAD租户的名字替换掉`tenant`。
7. 使用Azure管理门户中客户端ID的值替换掉`clientId`。

### 第七步： 运行示例。

清除解决方案，重建解决方案，然后运行它。

您可以点击页面右上角的sign in超链接来触发登录的体验，或者直接点击To Do List 或者 To Go List标签。通过登录浏览示例，给To Do List添加数据，移除用户账号，然后重新开始。给To Go List添加地址，对采用CORS的To Go API执行CRUD操作。

## 如何部署这个示例到Azure

为了部署To Do SPA 和 To Go API到Azure网站应用，您需要创建两个网站，然后将项目分别发布，然后更新项目使用新的地址替换掉IIS Express。

### 创建To Go API Azure网站

1. 登录[门户网站](https://manage.windowsazure.cn)。
2. 点击左侧导航栏中的WEB 应用。
3. 点击左下角的“新建”按钮，选择计算--> WEB 应用 --> 自定义创建，输入您网站的名字，例如：togo-contoso.chinacloudsites.cn,选择APP SERVICE计划，选择使用的数据库，或者新建一个。点击“完成”创建网站。
4. 当网站创建好，点击来管理它。对于此组步骤，下载 发布配置文件然后保存。也可以使用其他一些部署机制，例如从源代码管理发布。

### 创建To Go SPA Azure网站

### Create the To Do SPA Azure Web Site

1. 登录[门户网站](https://manage.windowsazure.cn)。
2. 点击左侧导航栏中的WEB 应用。
3. 点击左下角的“新建”按钮，选择计算--> WEB 应用 --> 自定义创建，输入您网站的名字，例如：togo-contoso.chinacloudsites.cn,选择APP SERVICE计划，选择使用的数据库，使用和To Go API一样的就好了。点击“完成”创建网站。
4. 当网站创建好，点击来管理它。对于此组步骤，下载 发布配置文件然后保存。

### 为使用Azure网站更新项目

1. 在Visual Studio中，定位到TodoSPA项目。
2. 需要改变两处。在`App\Scripts\app.js`文件中，使用您To Go API新的地址来替换掉`endpoints`对象属性的值，例如`https://togo-contoso.chinacloudsites.cn/`。在`App\Scripts\toGoListSvc.js`中使用同样的值替换掉 `apiEndpoint`变量。
3. 在ToGoAPI项目中，只需要改变一处。在`Controllers\ToGoListController.cs`中更新`[EnableCors...]`特性映射的值，例如`https://togo-contoso.chinacloudsites.cn`。再次强调，务必忽略最后的斜杠。

### 发布To Go API到Azure网站

1. 切换到Visual Studio，定位到ToGoAPI项目，右击解决方案中的该项目，选择“Publish”。点击“Import”，然后倒入之前下载的To Go API的发布配置文件。
2. 在“Connection”标签上，更新目标URL使用https，例如`https://togo-contoso.chinacloudsites.cn/`。点击下一步。
3. 在“Settings”标签中，确保“Enable Organizational Authentication”没有被选中，点击发布。
4. Visual Studio将发布项目，完成后会自动打开浏览器并浏览发布的项目。如果您看到项目默认的页面，发布就成功了。

### 发布To Go SPA到Azure网站

1. 切换到Visual Studio，定位到ToGoAPI项目，右击解决方案中的该项目，选择“Publish”。点击“Import”，然后倒入之前下载的To Go SPA的发布配置文件。
2. 在“Connection”标签上，更新目标URL使用https，例如`https://togo-contoso.chinacloudsites.cn/`。点击下一步。
3. 在“Settings”标签中，确保“Enable Organizational Authentication”没有被选中，点击发布。
4. Visual Studio将发布项目，完成后会自动打开浏览器并浏览发布的项目。如果您看到项目默认的页面，发布就成功了。

### 在Azure AD租户中更新To Do SPA配置

1. 导航到[门户网站](https://manage.windowsazure.cn)。
2. 左侧导航栏中点击Active Directory，然后选择您的租户。
3. 在程序列表页，选择To Do SPA 程序。
4. 在配置标签中，使用您SPA的新地址更新登录URL和回复URL,例如`https://togo-contoso.chinacloudsites.cn/`。保存配置。

## 关于代码

下面的文件包含了认证的主要逻辑：

**App.js** - 注入ADAL依赖模块，提供程序配置的值用来推动与AAD协议交互，并指示哪些路由没有认证是不可以被访问的。

**index.html** - 包含 adal.js的引用

**HomeController.js**- 展示如何在ADAL中利用 login() 和 logOut()方法。

**UserDataController.js** - 展示如何从缓存的id_token中提取用户的信息。

特别感谢@matvelloso协助完成这个示例。
