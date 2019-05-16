## css3动画样式总结

### transition属性

**语法格式**

**transition**:  要过渡的属性	花费时间		运动曲线 	何时开始

| 属性                       | 描述                                         |
| -------------------------- | -------------------------------------------- |
| transition                 | 简写属性，用于在一个属性中设置四个过渡属性。 |
| transition-property        | 规定应用过渡的CSS属性的名称。                |
| transition-duration        | 定义过渡效果花费的时间。默认是0。            |
| transition-trming-function | 规定过渡效果的时间曲线。默认是“ease"。       |
| transition-delay           | 规定过度效果何时开始。默认是0。              |

**运动曲线示意图**

**透视效果**

```css
transform: perspective(500px);
```

**反转效果**

```css
transform-origin: left; // 反转轴
transform: rotateX(-130deg); 
transform: rotateY(130deg); 
transform: rotateZ(130deg); 
```

**旋转**

```css
transform: rotate(360deg);
```

**延时效果**

```css
transition: all 0.5s;
```