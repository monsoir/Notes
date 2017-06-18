# 使用 AVFoundation 进行视频录制

这里的视频录制，录制的视频只是直接存放到文件中

- [画大饼 - 录制配置过程](#画大饼)
- [配置视频输入](#配置视频输入)
- [配置音频输入](#配置音频输入)
- [配置 Session](#配置-session)
- [配置输出](#配置输出)
- [写一个总的配置过程](#写一个总的配置过程)
	- [配置输入](#配置输入)
	- [配置输出和预览界面](#配置输出和预览界面)
- [开始录制](#开始录制)
- [停止录制](停止录制)
- [注意事项](#注意事项)
- [References](#references)

## 画大饼

![](https://ws2.sinaimg.cn/large/006tKfTcgy1fgp9dp53v9j30m90h5q49.jpg)

使用 AVFoundation 可以说是挺有模块化思想的：

1. 创建一个视频输入(AVCaptureDevice + AVCaptureDeviceInput)
2. 创建一个音频输入(AVCaptureDevice + AVCaptureDeviceInput)
3. 创建一个音频视频的输出(AVCaptureMovieFileOutput)
4. 将视频输入和音频输入，音频视频输出添加到一个叫 Session 的东西里面进行统一的管理(AVCaptureSession)

因此，数据的流动方向将是：

(图像, 声音) -> AVCaptureDevice -> AVCaptureDeviceInput -> AVCaptureSession -> AVCaptureMovieFileOuput -> (文件)

## 配置视频输入

```swift
func setupCameraInput() throws -> AVCaptureDeviceInput {
        
   let cameraDevice = AVCaptureDevice.default(for: .video)
   
   do {
       let cameraInput = try AVCaptureDeviceInput(device: cameraDevice!)
       return cameraInput
   } catch {
       throw error
   }
}
```

以上配置使用了视频输入的默认设置

通过 `AVCaptureDevice.default(for: .video)` 获得硬件的访问对象，再通过这个对象创建一个软件上的对象

## 配置音频输入

```swift
func setupMicInput() throws -> AVCaptureDeviceInput {
   let micDevice = AVCaptureDevice.default(for: .audio)
   
   do {
       let audioInput = try AVCaptureDeviceInput(device: micDevice!)
       return audioInput
   } catch {
       throw error
   }
}
```

以上配置使用了音频输入的默认设置

通过 `AVCaptureDevice.default(for: .audio)` 获得硬件的访问对象，再通过这个对象创建一个软件上的对象

## 配置 Session

```swift
func setupCaptureSession() throws -> AVCaptureSession {
        
   do {
       let cameraInput = try setupCameraInput()
       let micInput = try setupMicInput()
       
       let captureSession = AVCaptureSession()
       if captureSession.canAddInput(cameraInput) {
           captureSession.addInput(cameraInput)
       }
       
       if captureSession.canAddInput(micInput) {
           captureSession.addInput(micInput)
       }
       
       return captureSession
   } catch {
       throw error
   }
}
```

以上过程，将视频和音频的输入配置到了一个 Session 中

需要注意的是，对 session 的调用都是阻塞式的，因此，调用有关 session API 的过程，应该在一个后台的串行队列中进行，即，以上的配置过程，应该在一个后台的串行队列中进行

## 配置输出

由于配置视频和音频时，有可能会抛出异常，因此，这里会在当视频和音频成功配置后，才会再进行输出的配置

```swift
self.fileOutput = AVCaptureMovieFileOutput()
if self.captureSession.canAddOutput(self.fileOutput!) {
	self.captureSession.addOutput(self.fileOutput!)
}
```

配置视频的文件输出比较简单，只需要创建一个对象，并添加到 session 中即可

## 写一个总的配置过程

### 配置输入

```swift
func prepareCapture(completion: @escaping (Bool, AVCaptureSession?, Error?) -> Void) {
   serialQ.async { [unowned self] in
       do {
           let captureSession = try self.setupCaptureSession()
           completion(true, captureSession, nil)
       } catch {
           completion(false, nil, error)
       }
   }
}
```

---

serialQ 是一个后台的串行队列，用来进行有关 session 的操作，避免阻塞

`let serialQ = DispatchQueue(label: "com.wenyongyang.createCaptureSession")`

---

completion 是一个异步回调，在配置输入完成后进行回调，无论成功或失败

### 配置输出和预览界面

```swift
prepareCapture { [unowned self] (success, session, error) in
  if success {
      self.captureSession = session!
      self.fileOutput = AVCaptureMovieFileOutput()
      if self.captureSession.canAddOutput(self.fileOutput!) {
          self.captureSession.addOutput(self.fileOutput!)
      }
      self.captureSession.startRunning()
      
      let previewLayer = AVCaptureVideoPreviewLayer(session: self.captureSession)
      DispatchQueue.main.async {
          previewLayer.frame = self.view.bounds
          self.view.layer.insertSublayer(previewLayer, below: self.btnStart.layer)
      }
  } else {
      print("\(error!.localizedDescription)")
  }
}
```

---

回调参数 `(success, session, error)`

success 指示输入配置是否成功，true 为成功，否则 false

session 为 optional，若输入配置成功，则是一个 AVCaptureSession 对象，否则为 nil

error 为 optional，若输入配置成功，则为 nil，否则会是配置输入时抛出的异常

---

若输入配置成功，将会接着配置输出

并且，将会创建一个用于预览的 layer

```swift
let previewLayer = AVCaptureVideoPreviewLayer(session: self.captureSession)
DispatchQueue.main.async {
	previewLayer.frame = self.view.bounds
	self.view.layer.insertSublayer(previewLayer, below: self.btnStart.layer)
}
```

---

`self.captureSession.startRunning()` 这一句，并不是真正的录制开始，而只是开始捕捉画面并显示在预览层上

## 开始录制

```swift
serialQ.async { [unowned self] in
  let saveURL = self.savePath()
  if !self.captureSession.isRunning { self.captureSession.startRunning() }
  self.fileOutput?.startRecording(to: saveURL, recordingDelegate: self)
}
```

要进行真正的录制，即将视频存储到了文件中，需要调用输出对象(AVCaptureMovieFileOutput)的方法，同时还需要指定存储的路径

---

recordingDelegate 的协议是 `AVCaptureFileOutputRecordingDelegate`

在这个协议中，我们可以获悉到录制的开始与结束的时机，如

```swift
/// 录制开始
func fileOutput(_ output: AVCaptureFileOutput, didStartRecordingTo fileURL: URL, from connections: [AVCaptureConnection]) {
   print("start")
}

/// 录制结束
func fileOutput(_ output: AVCaptureFileOutput, didFinishRecordingTo outputFileURL: URL, from connections: [AVCaptureConnection], error: Error?) {
   print("\(outputFileURL)")
   print("finish")
}
```

## 停止录制

```swift
serialQ.async { [unowned self] in
  self.captureSession.stopRunning()
  self.fileOutput?.stopRecording()
}
```

## 注意事项

- 摄像头的隐私访问描述字段 -> NSCameraUsageDescription
- 麦克风的隐私访问描述字段 -> NSMicrophoneUsageDescription
- 当需要改变 session 的配置时，如改变录制质量等操作时，需要调用 `lockForConfiguration()` 来独占访问权限，并使用 `unlockForConfiguration()` 进行释放权限
- 当用户未授权时，对于录制视频或音频的尝试，得到的将是黑色画面和无声
- 存储路径，需要以 `file://` 开头

## 额外补充

### 指定录制时长

设置 AVCaptureMovieFileOutput 属性 maxRecordedDuration，默认为不限制

注意的是，maxRecordedDuration 的类型是 CMTime

其中的一个初始化方法 `init(seconds: Double, preferredTimescale: CMTimeScale)`

其计算形式是 `int64/int32`，即 seconds / preferredtimescale，可以看作是分数，因此当以秒来做单位是，可以置 [preferredTimescale 为 1

## References

[在 iOS 上捕获视频](https://www.objccn.io/issue-23-1/)

