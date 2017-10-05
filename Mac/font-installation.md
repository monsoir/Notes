# 字体安装

## Consolas

Consolas 是微软出品的字体，如果安装过 Microsoft Office for Mac 的话，就会自动安装这个字体，否则，需要自己安装，安装通过 Homebrew 

```sh
brew install cabextract

# 下载安装包
curl -O http://download.microsoft.com/download/f/5/a/f5a3df76-d856-4a61-a6bd-722f52a5be26/PowerPointViewer.exe

cabextract PowerPointViewer.exe
cabextract ppviewer.cab
open CONSOLA*.TTF
```

最后，会打开一个窗口，之间点击 Install Font 即可

