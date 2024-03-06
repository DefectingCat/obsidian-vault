## Install

```bash
yay -S fcitx5-im
yay -S fcitx5-chinese-addons 
```

在 gnome shell 显示输入法的状态，以及适配 gnome 的主题需要额外一个 gnome 的拓展 [kimpanel](https://extensions.gnome.org/extension/261/kimpanel/)

一些必须的环境变量

```
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
INPUT_METHOD=fcitx
SDL_IM_MODULE=fcitx
GLFW_IM_MODULE=ibus
```