---

license: Licensed to the Apache Software Foundation (ASF) under one or more contributor license agreements. See the NOTICE file distributed with this work for additional information regarding copyright ownership. The ASF licenses this file to you under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at

           http://www.apache.org/licenses/LICENSE-2.0
    
         Unless required by applicable law or agreed to in writing,
         software distributed under the License is distributed on an
         "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
         KIND, either express or implied.  See the License for the
         specific language governing permissions and limitations
    

   under the License.
---

# capture.captureVideo

> 启动视频录制器应用程序并返回有关捕获的视频剪辑文件的信息。

    navigator.device.capture.captureVideo(
        CaptureCB captureSuccess, CaptureErrorCB captureError, [CaptureVideoOptions options]
    );
    

## 说明

开始异步操作以捕获使用该设备的视频录制应用程序的视频录制。该操作允许用户在一个会话中捕获多个录音。

在捕获操作结束时或者在用户退出视频录制应用程序或由指定的录音的最大数目 `CaptureVideoOptions.limit` 到达。 如果没有 `limit` 指定参数的值，它将默认为一 (1) 和捕获操作终止后用户记录单个视频剪辑。

在捕获操作完成后，它 `CaptureCB` 回调执行与数组的 `MediaFile` 对象描述每个捕获视频剪辑文件。 如果用户终止之前捕获的视频剪辑，操作 `CaptureErrorCB` 回调执行与 `CaptureError` 对象特色 `CaptureError.CAPTURE_NO_MEDIA_FILES` 错误代码。

## 支持的平台

*   Android 系统
*   黑莓手机 WebWorks （OS 5.0 和更高）
*   iOS
*   Windows Phone 7 和 8
*   Windows 8

## 快速的示例

    // capture callback
    var captureSuccess = function(mediaFiles) {
        var i, path, len;
        for (i = 0, len = mediaFiles.length; i < len; i += 1) {
            path = mediaFiles[i].fullPath;
            // do something interesting with the file
        }
    };
    
    // capture error callback
    var captureError = function(error) {
        navigator.notification.alert('Error code: ' + error.code, null, 'Capture Error');
    };
    
    // start video capture
    navigator.device.capture.captureVideo(captureSuccess, captureError, {limit:2});
    

## 完整的示例

    <!DOCTYPE html>
    <html>
      <head>
        <title>Capture Video</title>
    
        <script type="text/javascript" charset="utf-8" src="cordova.js"></script>
        <script type="text/javascript" charset="utf-8" src="json2.js"></script>
        <script type="text/javascript" charset="utf-8">
    
        // Called when capture operation is finished
        //
        function captureSuccess(mediaFiles) {
            var i, len;
            for (i = 0, len = mediaFiles.length; i < len; i += 1) {
                uploadFile(mediaFiles[i]);
            }
        }
    
        // Called if something bad happens.
        //
        function captureError(error) {
            var msg = 'An error occurred during capture: ' + error.code;
            navigator.notification.alert(msg, null, 'Uh oh!');
        }
    
        // A button will call this function
        //
        function captureVideo() {
            // Launch device video recording application,
            // allowing user to capture up to 2 video clips
            navigator.device.capture.captureVideo(captureSuccess, captureError, {limit: 2});
        }
    
        // Upload files to server
        function uploadFile(mediaFile) {
            var ft = new FileTransfer(),
                path = mediaFile.fullPath,
                name = mediaFile.name;
    
            ft.upload(path,
                "http://my.domain.com/upload.php",
                function(result) {
                    console.log('Upload success: ' + result.responseCode);
                    console.log(result.bytesSent + ' bytes sent');
                },
                function(error) {
                    console.log('Error uploading file ' + path + ': ' + error.code);
                },
                { fileName: name });
        }
    
        </script>
        </head>
        <body>
            <button onclick="captureVideo();">Capture Video</button> <br>
        </body>
    </html>
    

## 黑莓 WebWorks 怪癖

*   科尔多瓦的黑莓 WebWorks 尝试启动**视频录像机**提供的应用程序，由 RIM，以捕获视频的录制。 这款应用程序会收到 `CaptureError.CAPTURE_NOT_SUPPORTED` 错误代码，如果应用程序未安装在设备上。