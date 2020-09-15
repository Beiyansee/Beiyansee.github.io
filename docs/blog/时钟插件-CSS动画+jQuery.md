### 时钟插件-CSS动画+jQuery

> 使用HTML，CSS，SVG背景和一些jquery创建的时钟。使用CSS动画和过渡进行移动，使用jquery来设置初始时间并添加基本的CSS变换

### 最终效果展示

![clock](https://zlm-blog-img.oss-cn-beijing.aliyuncs.com/clock.gif)



### 首先创建一个div用来展示生成的时钟

```html
<body>
  <div class="home-page">
    <div id="clock-box"></div>
  </div>
</body>
```

### 需要引入jQuery

```js
<script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
```

### 编写js组件

```js
var clock = {
  div: null,
  id: null,
  init: function (id) {
    this.div = $(id);
    this.id = id;
    this.style();
    this.move();
    this.bind();
    return this;
  },
  style: function () {
    this.div.html("<div id='clock'>" +
      "<div class='num-container'><span class='clock-num'></span></div>" +
      "<div class='hours-container'><div class='hours'></div></div>" +
      "<div class='minutes-container'><div class='minutes'></div></div>" +
      "<div class='seconds-container'><div class='seconds'></div></div></div>");
  },
  bind: function () {
    setInterval("clock.render()", 1000);
  },
  render: function () {
    var time = new Date();
    var h = this.num(time.getHours());
    var m = this.num(time.getMinutes());
    var s = this.num(time.getSeconds());
    $("#clock .clock-num").html(h + ":" + m + ":" + s);
  },
  move: function () {
    var time = new Date();
    var h = time.getHours();
    var m = time.getMinutes();
    var s = time.getSeconds();
    var rate_s = s * 6;
    var rate_m = m * 6 + rate_s / 360;
    var rate_h = h % 12 * 30 + rate_m / 360;
    console.log(rate_h.toFixed(2) + "-" + rate_m.toFixed(2) + "-" + rate_s);
    $("#clock .seconds").css('transform', 'rotate(' + rate_s + 'deg)');
    $("#clock .minutes").css('transform', 'rotate(' + rate_m + 'deg)');
    $("#clock .hours").css('transform', 'rotate(' + rate_h + 'deg)');
  },
  //小于10补0
  num: function (num) {
    return num < 10 ? '0' + num : num;
  }
}
```

### 编写CSS样式

```css
#clock {
  width: 20em;
  height: 20em;
  background: #fff url(../img/clock.svg) no-repeat center;
  border-radius: 50%;
  background-size: 88%;
  position: relative;
}

#clock:after {
  background: #000;
  border-radius: 50%;
  content: "";
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  width: 5%;
  height: 5%;
  z-index: 10;
}

.minutes-container,
.hours-container,
.seconds-container {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
}

.num-container {
  position: absolute;
  right: 0;
  bottom: 0;
  background: #000;
  color: #FFF;
  padding: 3px;
}

.hours {
  background: #000;
  height: 20%;
  left: 48.75%;
  position: absolute;
  top: 30%;
  transform-origin: 50% 100%;
  width: 2.5%;
}


.minutes {
  background: #000;
  height: 30%;
  left: 49%;
  position: absolute;
  top: 20%;
  transform-origin: 50% 100%;
  width: 2%;
}

.seconds {
  background: #000;
  height: 45%;
  left: 49.5%;
  position: absolute;
  top: 14%;
  transform-origin: 50% 80%;
  width: 1%;
  z-index: 8;
}

@keyframes rotate {
  100% {
    transform: rotateZ(360deg);
  }
}

.hours-container {
  animation: rotate 43200s infinite linear;
}

.minutes-container {
  animation: rotate 3600s infinite linear;
}

.seconds-container {
  animation: rotate 60s infinite steps(60);
}
```

### 初始化时钟

```js
<script>
  $(function () {
    clock.init("#clock-box").render();
  })
</script>
```

### 运行代码就可以看到效果了

![clock](https://zlm-blog-img.oss-cn-beijing.aliyuncs.com/clock.gif)

源代码：https://download.csdn.net/download/qq_37918553/12832785

