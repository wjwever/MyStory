# [vim-surround插件](https://github.com/wjwever/gitblog/issues/31)

## 简介
生命在于折腾，没事鼓捣一下vim，今天发现的一个杀手级插件是vim-surround，这个插件可以快速对一段文本加上扩号，引号等等，简直是编程大杀器。先来看看网有的评论吧：
> ["vim-surround](https://github.com/tpope/vim-surround)，tpope大神的一款life-changed插件."
> "直到发现了大牛[Tim](http://www.vim.org/account/profile.php?user_id=9012) Pope的[surround](https://github.com/tpope/vim-surround)的插件，一切都迎刃而解了。（对的就是那个写了[pathogen](https://github.com/tpope/vim-pathogen)的家伙，他一个人就贡献了30多个vim插件，仰视之）"
## 安装
>项目的地址是：https://github.com/tpope/vim-surround
如果采用Vundle安装的话，就需要在vim配置文件中添加：
Plugin 'tpope/vim-surround'
再执行：PluginInstall 就好了。
## 使用
这个插件功能还是十分强大的，目前我只了解了他的基本功能，不过根据28原则，这也应该够了，后续功能再慢慢补充～
首先在vimrc中对该插件进行配置如下：
```
    vmap " S"
    vmap ' S'
    vmap ` S`
    vmap [ S[
    vmap ( S(
    vmap { S{
    vmap } S}
    vmap ] S]
    vmap ) S)
    vmap > S>
```
比如下面代码我想快速在 iostream加上<>那么可以这样操作：
* ve 选中iostream这个单词
* \>给iostream加上尖括号
![image](https://github.com/wjwever/gitblog/assets/50772316/79891a7d-f463-4be3-aaa7-7fcecd37c64f)
