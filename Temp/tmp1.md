

### 基本样式封装

```less
@color-main: #FF6A00;
@color-active: #FF5500;
// 基本样式封装，命名与业务逻辑无关
.lt-btn {
    box-sizing: border-box;
    outline: none;
    margin: 0;
    padding: 10px;
    font-size: 14px;
    text-align: center;
    height: 30px;
    text-decoration: none;
    background: @color-main;
    border: 1px solid @color-main;
    color: #FFF;
    border-radius: 4px;
    
    &:hover {
        backgroud: @color-active;
    }
    
    // 线框按钮
    &.lt-btn-outline {
        background: #FFF;
        color: @color-main;
        
        &:hover {
             background: @color-main;
             color: #FFF;
        }
    }
    
    // 圆形按钮
    &.lt-btn-round {
        border-radius: 50%;
    }
    
    // 小按钮
    &.lt-btn-sm {
        ...
    }
    
    // 大按钮
    &.lt-btn-lg {
        ...
    }
}
```



### 使用

```html
<button class="lt-btn">
    默认按钮
</button>
<button class="lt-btn lt-outline">
    线框按钮
</button>
<button class="lt-btn lt-btn-sm lt-outline">
    小线框按钮
</button>
<button class="lt-btn lt-btn-round">
    圆角按钮
</button>
<button class="lt-btn lt-outline lt-btn-round">
    圆角线框按钮
</button>

<button class="lt-btn lt-outline lt-btn-round good-btn">
    样式重写按钮
</button>
<style lang="lessa">
    /* 特殊按钮需要重写样式，命名可以和业务关联 */
    .good-btn {
        border-radius: 0;
        width: 100px;
        height: 20px;
    }
</style>
```

