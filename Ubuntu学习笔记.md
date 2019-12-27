# Ubuntu学习笔记
## 常用更新命令
```bash
// 检测可更新的软件
apt update
// 自动更新并安装软件
apt upgrade
// 检测并进行系统升级
apt dist-upgrade
```
## 常用清理命令
```bash
// 卸载过期的软件包
apt autoremove 
// 清理无用软件包
apt autosudo
// 清理缓存
apt autoclean
// 强制清理缓存
apt clean
```
## apt-get 常用实例
```bash
apt-cache search packagename 搜索包
apt-cache show packagename 获取包的相关信息，如说明、大小、版本等
apt-get install packagename 安装包
apt-get install packagename –reinstall 重新安装包
apt-get -f install 修复安装”-f = –fix-missing”
apt-get remove packagename 删除包
apt-get remove packagename –purge 删除包，包括删除配置文件等
apt-get update 更新源
apt-get upgrade 更新已安装的包
apt-get dist-upgrade 升级系统
apt-get dselect-upgrade 使用 dselect 升级
apt-cache depends packagename 了解使用依赖
apt-cache rdepends packagename 是查看该包被哪些包依赖
apt-get build-dep packagename 安装相关的编译环境
apt-get source packagename 下载该包的源代码
apt-get clean 清理无用的包
apt-get autoclean 清理无用的包
apt-get check 检查是否有损坏的依赖
```