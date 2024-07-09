---
title: CSS+Vanilla-tilt.js的悬浮卡片
date: 2021-12-06 11:59:29
tags: 偷懒用
categories: CSS
---

> 参考来源：https://www.bilibili.com/video/BV1C3411t7cc

# 注意

使用FireFox进行调试的时候，发现FireFox好像不支持`backdrop-filter`这个属性，所以想看背景模糊效果的话请用Chrome

# 效果图

![悬浮卡片](https://cdn.jsdelivr.net/gh/SC-WSKun/HexoStaticFile/img/%E6%82%AC%E6%B5%AE%E5%8D%A1%E7%89%87.gif)

# HTML

```html
<!DOCTYPE html>
<html>
  <head>
    <title>悬浮卡片</title>
    <link type="text/css" rel="stylesheet" href="fillCard.css" />
  </head>
  <body>
    <div class="container">
      <div class="card">
        <div class="content">
          <h2>01</h2>
          <h3>Card One</h3>
          <p>
            aaaaaaaaaaaaaaaaaaaaaaa
            aaaaaaaaaaaaaaaaaaaaaaa
            aaaaaaaaaaaaaaaaaaaaa
          </p>
          <a href="#">Read More</a>
        </div>
      </div>
      <div class="card">
        <div class="content">
          <h2>02</h2>
          <h3>Card Two</h3>
          <p>
            aaaaaaaaaaaaaaaaaaaaaaaa
            aaaaaaaaaaaaaaaaaaaaaaaa
            aaaaaaaaaaaaaaaaaaaaa
          </p>
          <a href="#">Read More</a>
        </div>
      </div>
      <div class="card">
        <div class="content">
          <h2>03</h2>
          <h3>Card Three</h3>
          <p>
            aaaaaaaaaaaaaaaaaaaaaaa
            aaaaaaaaaaaaaaaaaaaaaaa
            aaaaaaaaaaaaaaaaaaaaa
          </p>
          <a href="#">Read More</a>
        </div>
      </div>
    </div>
      
    <!-- 这里是实现卡片反光效果跟旋转的核心，调用了vanilla-tilt这个库 -->
    <script type="text/javascript" src="vanilla-tilt.js"></script>
    <script>
    VanillaTilt.init(document.querySelectorAll(".card"), {
		max: 25,
		speed: 400,
        glare:true,
        "max-glare": 1
	});
    </script>
    <!--  -->
  </body>
</html>

```

# CSS

```css
@import url("https://fonts.googleapis.com/css?family=Poppins:200,300,400,500,600,700,800,900&display=swap");
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Poppins", sans-serif;
}
body {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background: #161623;
}

body::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(#f00, #f0f);
  /* 比起用border-radius设置圆形，再做个定位移动到指定位置，使用clip-path显然更高效 */
  clip-path: circle(30% at right 70%);
}
body::after {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(#2196f3, #e91e63);
  clip-path: circle(20% at 10% 10%);
}

.container{
    position: relative;
    display: flex;
    justify-content: center;
    align-items: center;
    max-width: 1200px;
    flex-wrap: wrap;
    z-index: 1;
}

.container .card{
    position: relative;
    width: 280px;
    height: 400px;
    margin: 30px;
    box-shadow: 20px 20px 50px rgba(0, 0, 0, 0.5);
    border-radius: 15px;
    background: rgba(255, 255, 255, 0.1);
    overflow: hidden;
    display: flex;
    justify-content: center;
    align-items: center;
    border-top: 1px solid rgba(255, 255, 255, 0.5);
    border-left: 1px solid rgba(255, 255, 255, 0.5);
    /* 这个是比较惊艳的地方，正常用filter会连带着内容一起模糊 */
    /* 用backdrop-filter的话可以只模糊背景，做出毛玻璃的感觉 */
    backdrop-filter: blur(5px);
}

.container .card .content{
    padding: 20px;
    text-align: center;
    /* 另一个学到的地方，使用hover伪元素和opacity跟位移的渐变，实现渐入渐出的效果 */
    transform: translateY(100px);
    opacity: 0;
    transition: 0.5s;
}

.container .card:hover .content{
    transform: translateY(0px);
    opacity: 1;
}

.container .card .content h2{
    position: absolute;
    top: -40px;
    right: 30px;
    font-size: 8em;
    color: rgba(255, 255, 255, 0.05);
    pointer-events: none;
}

.container .card .content h3{
    font-size: 1.8em;
    color: #fff;
    z-index: 1;
}

.container .card .content p{
    font-size: 1em;
    color: #fff;
    font-weight: 300;
    
}
.container .card .content a{
    position: relative;
    display: inline-block;
    padding: 8px 20px;
    margin-top: 15px;
    background: #fff;
    color: #000;
    border-radius: 20px;
    text-decoration: none;
    font-weight: 500;
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);

}
```

