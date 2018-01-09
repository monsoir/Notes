# Visual Studio Code 配置 Laravel 开发/调试环境

## 需要的工具

- 已安装好 Laravel, Homestead, Vagrant 的开发环境
- Visual Studio Code
- Chrome 插件
    - XDebug helper
- Visual Studio Code 插件
    - PHP Debug 调试必须
    - Laravel Helpers
    - laravel-blade
    - PHP Intellisense

以下内容主要涉及调试配置部分

## 关于 XDebug

Laravel 的 Homestead vagrant box 已经内置 XDebug, 因此，使用 Homestead 的话就不用担心了，如果没有安装，则参考 [「用 Visual Studio Code 调试运行在 homestead 环境中的 laravel 程序」](http://www.cnblogs.com/kelsen/p/6368550.html)

## 关于浏览器

在浏览器中，需要安装额外的插件来支持 XDebug, 若使用 Chrome, 则安装 XDebug helper

其他浏览器，参考 [How to Set Up Xdebug on Laravel Homestead and VSCode](https://www.patrickstephan.me/post/how-to-set-up-xdebug-on-laravel-homestead-and-vscode.html)

## 配置 Visual Studio Code

需要安装 PHP Debug 插件

安装完成后

1. 打开 Visual Studio Code 的调试界面

    点击齿轮，并选择 PHP 环境

    ![](https://www.patrickstephan.me/images/post-images/xdebug-vscode/debugger.jpg)

2. 配置 launch.json

    ```json
    {
        // Use IntelliSense to learn about possible attributes.
        // Hover to view descriptions of existing attributes.
        // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
        "version": "0.2.0",
        "configurations": [
            {
                "name": "Listen for XDebug",
                "type": "php",
                "request": "launch",
                "port": 9000,
                "pathMappings": {
                    "/home/vagrant/Code/Laravel": "${workspaceRoot}",
                }
            },
            {
                "name": "Launch currently open script",
                "type": "php",
                "request": "launch",
                "program": "${file}",
                "cwd": "${fileDirname}",
                "port": 9000
            }
        ]
    }
    ```
    
    - `configurations` 数组中，第一组配置是用来监听 XDebug 并打开 XDebug 返回的文件；第二组配置是使用 XDebug 运行单一的一个文件，可以忽略
    - `pathMappings` 是用来映射虚拟机与主机之间的共享文件夹，方向为 `虚拟机: 主机`
        - 老版本的 vscode 用来映射的虚拟机和主机字段分别是 `serverSource"Root` 和 `localSourceRoot`

3. 到了最后，就可以像平常 debug 一样，打断点 debug 了

## 可能出现的问题

> 可能出现的问题？
> 因为看过很多教程，居然没有包含这个问题，不知道是不是我这边特别一点了

`Encrypter.php` 的 191 行代码可能会发生异常，就是那个 throw 了

```php
if (! $this->validPayload($payload)) {
    throw new DecryptException('The payload is invalid.');
}
```

将红框中的 Everything 断点去掉

![](https://ws4.sinaimg.cn/large/006tNc79gy1fnanye5gm5j30ay03u0sr.jpg)

当然，这是一种取巧，更进一步的做法，参考 [「用 Visual Studio Code 调试运行在 homestead 环境中的 laravel 程序」](http://www.cnblogs.com/kelsen/p/6368550.html), 但目前还没有尝试过

## References

- [Visual Studio Code Environment For Laravel](https://medium.com/@ardanirohman/laravel-%EF%B8%8F-visual-studio-code-debug-environment-65440274d3b0)
- [How to Set Up Xdebug on Laravel Homestead and VSCode](https://www.patrickstephan.me/post/how-to-set-up-xdebug-on-laravel-homestead-and-vscode.html)
- [用 Visual Studio Code 调试运行在 homestead 环境中的 laravel 程序](http://www.cnblogs.com/kelsen/p/6368550.html)

