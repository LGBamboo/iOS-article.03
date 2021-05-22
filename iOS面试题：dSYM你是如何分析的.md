# dSYM你是如何分析的？

**方法1 使用XCode**

这种方法可能是最容易的方法了。

1. 要使用Xcode符号化 crash log，你需要下面所列的3个文件：
2. crash报告（.crash文件）
3. 符号文件 (.dsymb文件)
4. 应用程序文件 (appName.app文件，把IPA文件后缀改为zip，然后解压，Payload目录下的appName.app文件), 这里的appName是你的应用程序的名称。
5. 把这3个文件放到同一个目录下，打开Xcode的Window菜单下的organizer，然后点击Devices tab，然后选中左边的Device Logs。
6. 然后把.crash文件拖到Device Logs或者选择下面的import导入.crash文件。
7. 这样你就可以看到crash的详细log了。

**方法2 使用命令行工具symbolicatecrash**

1. 有时候Xcode不能够很好的符号化crash文件。我们这里介绍如何通过symbolicatecrash来手动符号化crash log。
2. 在处理之前，请依然将“.app“, “.dSYM”和 ".crash"文件放到同一个目录下。现在打开终端(Terminal)然后输入如下的命令：
3. export DEVELOPER_DIR=/Applications/Xcode.app/Contents/Developer
4. 然后输入命令：
5. /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/Library/PrivateFrameworks/DTDeviceKitBase.framework/Versions/A/Resources/symbolicatecrash appName.crash appName.app > appName.log
6. 现在，符号化的crash log就保存在appName.log中了。

**方法3 使用命令行工具atos**

1. 如果你有多个“.ipa”文件，多个".dSYMB"文件，你并不太确定到底“dSYMB”文件对应哪个".ipa"文件，那么，这个方法就非常适合你。
2. 特别当你的应用发布到多个渠道的时候，你需要对不同渠道的crash文件，写一个自动化的分析脚本的时候，这个方法就极其有用。
3. 具体方法 请百度
