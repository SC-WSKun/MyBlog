---
title: HTML5音频可视化
date: 2021-12-06 15:39:44
tags: HTML5
categories: Web开发
---

# 一些API

## window.requestAnimationFrame()

在渲染动画时经常用到这个API，官方文档对于这个API的介绍是这样的：

> ***`window.requestAnimationFrame()`** 告诉浏览器——你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行*

这个方法能让回调函数的调用次数与浏览器的刷新率基本保持一致，一般为每秒60帧。

还有一个特点是，在隐藏或不可见的元素中，这个API不会进行重绘或回流，比起用setInterval，这个函数会占用更少的资源。

。。。

## HTML

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>test</title>
    <link rel="stylesheet" type="text/css" href="musicviewable.css" />
  </head>
  <body>
    <div style="background-color: whitesmoke; border-radius: 5px">
      <input id="file" type="file" oninput="onInputFileChange();" />
    </div>
    <canvas id="canva"></canvas>
    <video id="myVideo" width="520" height="360" controls autoplay loop>
      <source src="MetroExodus.mp4" />
    </video>
    <script>
      // 初始化
      var context = new (window.AudioContext || window.webkitAudioContext)();
      var analyser = context.createAnalyser();
      var source = null;
      var distortion = context.createWaveShaper();
      var gainNode = context.createGain();
      var biquadFilter = context.createBiquadFilter();
      var convolver = context.createConvolver();
      analyser.fftSize = 2048;

      var bufferLength = analyser.frequencyBinCount;
      var dataArray = new Uint8Array(bufferLength);

      //输入文件触发，将本地文件转成ArrayBuffer再进行解码
      function onInputFileChange() {
        var file = document.getElementById("file").files[0];
        var url = URL.createObjectURL(file);
        document.getElementById("myVideo").src = url;
        var fr = new FileReader();
        var filename = file.name;
        var soundSource;
        fr.readAsArrayBuffer(file);
        fr.onload = function (e) {
          var buf = e.target.result;
          console.log("buf", buf);
          context.decodeAudioData(
            buf,
            function (buffer) {
              soundSource = context.createBufferSource();
              soundSource.connect(analyser);
              analyser.connect(context.destination);
              soundSource.buffer = buffer;
              soundSource.loop = true;
              soundSource[soundSource.start ? "start" : "noteOn"](0);
            },
            function (e) {
              "Error with decoding audio data" + e.err;
            }
          );
        };
      }

      // 获取ID为 "canva" 的画布
      var canvas = document.getElementById("canva");
      var canvasCtx = canvas.getContext("2d");

      //绘制函数
      function draw() {
        drawVisual = requestAnimationFrame(draw);

        analyser.getByteTimeDomainData(dataArray);

        canvasCtx.fillStyle = "rgb(200,200,200)";
        canvasCtx.fillRect(0, 0, canvas.width, canvas.height);

        canvasCtx.lineWidth = 2;
        canvasCtx.strokeStyle = "rgb(0,0,0)";

        canvasCtx.beginPath();

        var sliceWidth = (canvas.width * 1.0) / bufferLength;
        var x = 0;
        for (var i = 0; i < bufferLength; i++) {
          var v = dataArray[i] / 128.0;
          var y = (v * canvas.height) / 2;

          if (i === 0) {
            canvasCtx.moveTo(x, y);
          } else {
            canvasCtx.lineTo(x, y);
          }

          x += sliceWidth;
        }

        canvasCtx.lineTo(canvas.width, canvas.height / 2);
        canvasCtx.stroke();
      }
      draw();
    </script>
  </body>
</html>

```

# CSS

```css
*{
    margin: 0;
    padding: 0;
}

body{
    display: grid;
    place-items: center;
    min-height: 100vh;
    background: #181818;
}

#myVideo{
    width: 320px;
    height: 176px;
}
```

