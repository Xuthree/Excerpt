# Windows 挂载 WebDAV | 糖醋鱼的小破站
[](#03cdfcc8ea024420b8286ddf0f4cf684 "方法一：推荐方式（raidrive）")方法一：推荐方式（[raidrive](https://www.raidrive.com/)）
---------------------------------------------------------------------------------------------------------

### [](#5ea57b387d3d4882ae835f6d1f52b402 "推荐原因")推荐原因

1.  此解决方案可以自定义缓存文件的存放位置。

2.  界面美观。

3.  配置简单。

![](https://oss.expoli.tech/img/RGN_image.png?t=09c39354-20d9-4408-8d1c-4137ee0fca7e)

### [](#bda598d24e344c2a96716e3a843357f7 "webdav 配置演示")webdav 配置演示

![](https://oss.expoli.tech/img/sao_webdav_newdrive.png?t=62427428-3c0e-40c0-a9c8-7c5655a452c9)

[](#00ecc29c1a7b4cb783cfa61ba925073e "方法二：使用文件管理器映射网络磁盘")方法二：使用文件管理器映射网络磁盘
--------------------------------------------------------------------------

1.  右键我的电脑->选择映射网络驱动器

2.  选择一个你喜欢的盘符（一般默认是Z）

3.  输入你的 WebDav 连接地址，例如: `https://example.com/nextcloud/remote.php/dav/files/USERNAME/`

💡

对于受SSL保护的服务器，请选中“登录时重新连接”以确保映射在以后的重新启动后是仍然可用。如果需要以其他用户身份连接到 WebDAV 服务器，请选中“使用其他凭据连接”。

![](https://oss.expoli.tech/img/qPK_explorer_webdav.png?t=c6720d26-4352-4c16-9618-902cca0fa2c6)

1.  点击完成即可在文件管理器中查看到所映射的网络驱动器。

![](https://oss.expoli.tech/img/VWX_dwV_image.png?t=4c4a6344-4ae4-4e6a-b17e-f3965c60baf2)

💡

注意！**因为WebDAV协议不支持查询硬盘容量，所以所挂载的硬盘容量与C盘容量一致！！**

[](#bae136a3578345e98bf0c215f9a166a0 "方法三：使用命令映射驱动器")方法三：使用命令映射驱动器
------------------------------------------------------------------

此方法在进行测试时发现根据Windows版本的不同、使用命令映射的网络驱动器无法在文件管理器中显示，可以尝试在任务管理器中重启 `explorer`。

1.  打开命令提示符窗口

2.  按照以下示例输入命令 `https://<drive_path>/remote.php/dav/files/USERNAME/` 为自己相应的WebDAV URL。

[](#221a4942c082489dbe0021ba17b85aa5 "已知的一些问题")已知的一些问题
------------------------------------------------------

### [](#8a77a15d58b844968ceab358bbf9a9e5 "问题一 Error 0x800700DF")问题一 Error 0x800700DF

> 您收到以下错误信息：错误0x800700DF：文件大小超出允许的限制，无法保存。

`Windows限制了从WebDAV共享传输或传输到WebDAV共享的文件的最大大小`。可以通过修改注册表 **HKEY\_LOCAL\_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\WebClient\\Parameters** 中的**FileSizeLimitInBytes** 值来解决。

要将限制增加到最大值4GB，请选择“**Decimal** 十进制”，输入值 `4294967295`，然后重新启动Windows或重新启动WebClient服务。

![](https://oss.expoli.tech/img/C9g_image.png?t=f7ff4574-97cc-4898-bafc-7bad779bacab)

![](https://oss.expoli.tech/img/BKU_image.png?t=c314b55a-f701-422b-ae8c-9121961986e5)

### [](#5375a061cd284976a572180070849779 "问题二 找不到网络名")问题二 找不到网络名

> 发生系统错误 67。

> 找不到网络名。

Solution：打开注册表编辑器，定位到`HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\WebClient\\Parameters`，修改键`BasicAuthLevel`值为`2`，即同时允许HTTP与HTTPS。