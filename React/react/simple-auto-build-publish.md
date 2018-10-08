# 简单的自动化构建与发布

这里的自动化构建与发布的意思是**通过一行命令来完成前端项目的构建以及将构建后的资源文件发送到前端服务器中，并能及时得到更新**

对于自动化构建项目工作，一般已经在 package.json 中有了 `npm run build` 命令，因此，只需要在自动化脚本添加对该命令的调用

而对于将构建后的资源文件推送到服务端的工作，将会采用 sftp 将资源文件推送到服务器

以上工作的脚本，将会使用 JavaScript 编写，结合 Gulp 工具进行

> 在 Node 中使用 sftp, 有第三方模块 `ssh2-sftp-client`, 但后来发现该模块暂时没有提供传送多个文件的接口，因此，改为使用 Gulp 进行脚本的编写，因为 `gulp-sftp` 的功能比较完善

## 使用到的库

- Node v10.11.0
    - 一切都基于 Node
- gulp 3.9.1
    - gulp 用于任务的管理
    - 项目构建，推送资源文件都是一个任务
- gulp-sftp 0.1.5
    - sftp 交互
- npx 10.2.0
    - 使得不用全局安装 gulp
- run-sequence 2.2.1
    - 串行管理任务，gulp 中的任务默认是并发

## 创建 Gulp 脚本

在项目的根目录下，创建 `gulpfile.js` 文件，Gulp 执行时会自动寻找该文件

常用的 Gulp API 有

```js
// 定义，注册一个任务
gulp.task('taskToRegister', (done) => {
  // do something
  done();
});
```

```js
// 将文件转换为流
gulp.src();
gulp.pipe();
```

## 自动化构建

在一般的项目中， package.json 中都已经有了 `npm run build` 或其他相似的命令用于构建生产环境的资源，我们只需要在 Gulp 脚本中调用该命令

实际上，`npm run build` 是一个 shell 命令，而在 Node 中调用 Shell 命令，需要新建一个子进程进行

```js
const gulp = require('gulp');
const { spawn } = require('child_process');

gulp.task('build', (callback) => {
  const buildTask = spawn('npm', ['run', 'build']); // Shell 命令就是这样子传进去了
  buildTask.stdout.on('data', (data) => {
    console.log(data.toString());
  });
  buildTask.stderr.on('data', (data) => {
    console.error(data.toString());
  });
  buildTask.on('exit', (code) => {
    console.log('build process exits');
    callback(code);
  });
  buildTask.on('error', (err) => {
    console.error(`build process error: ${err}`);
  });
});
```

在上面的代码中

- 注册了一个名叫 `build` 的任务，用于调用 `npm run build` 命令
- 使用 Node 内置的模块 `child_process` 中的 `spawn` 新建了一个子进程
- 同时对子进程的部分事件进行订阅，当事件为 `exit` 时，进行下一个任务的处理

## 将资源文件推送到前端服务器

```js
const gulp = require('gulp');
const sftp = require('gulp-sftp');

const publishConfig = require('./config/publish.config');

gulp.task('send', (callback) => {
  const stream = gulp.src('build/**').pipe(sftp(publishConfig.prod));
  return stream;
});
```

在上面的代码中

- 注册了一个名叫 `send` 的任务，用于将 `build` 目录下的文件推送到服务器中
- 任务的回调函数中返回了一个流对象，表示了这个任务是异步的任务
    - 一个异步的任务需要在回调函数中返回（其中一个）
        - 流对象
        - Promise 对象
        - 执行回调函数的函数 callback
- 使用了 `gulp.src()` 将 `build` 文件夹的内容转换为流对象，并通过 `gulp.pipe()` 管道将流一直流向 sftp
- 注意 `build/**` 中的匹配符，`*` 表示 `build` 文件夹第一层的内容，`**` 表示递归 `build` 文件夹下的内容，包含其子文件夹

## 合并任务

到此处，我们已经使用 `npx gulp build` 来进行自动化的构建，使用 `npx gulp send` 将资源文件推送到服务器

但我们还可以将两个任务合并成一个任务，实现通过一个命令实施两个任务

需要先安装第三方库 `run-sequence`, 它更好地控制串行任务

> 由于 gulp 中的任务是并发的，不能保证任务的完成先后顺序，而 `gulp.series` 需要在 Gulp 4.x 后才能使用，因此，使用 `run-sequence` 来控制任务的完成先后顺序
> 
> 任务的先后顺序应为 build -> send

```js
const runSequence = require('run-sequence');

// 这里的 callback 的传递可用于命令行中的统计
gulp.task('publish', (callback) => {
  runSequence('build', 'send', callback);
});
```

上面的代码

- 实现了 `build` 完成后再执行 `send` 任务
- callback 则是 `publish` 任务完成后的回调，这个回调中可以进行任务实施时长的统计，在所有任务的后面再进行回调

