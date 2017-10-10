# Mac

## 允许打开任何来源的 App

`sudo spctl --master-disable`

## 关闭允许打开任何来源的 App

`sudo spctl --master-enable`

## 创建软连接

```shell
ln -s <源文件> <目标文件>
```

## 制作 macOS 安装盘

```sh
sudo /Applications/<下载的安装包>/Contents/Resources/createinstallmedia --volume /Volumes/<U盘名称> --applicationpath /Applications/<下载的安装包> --nointeraction
```

