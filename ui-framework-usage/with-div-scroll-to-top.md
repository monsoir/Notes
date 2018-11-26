# Ant Design div 滚动到最顶部的高阶组件

## 组件定义

```js
import React from 'react';

/**
 * 通过 class 获取某个 div，并将提供将 div 滚动到最顶部的方法
 *
 * 滚动到顶部的调用方式：this.props.scrollToTop()
 * class 名称默认为 ant-layout-content
 * 使用例子见 GoodsManagement 最后一行
 * @param {*} divId div class 名字
 */
function WithDivScrollToTop(divId = 'ant-layout-content') {
  const innerWithDivScrollToTop = BaseComponent => (
    class extends React.Component {
      scrollToTop = () => {
        const div = document.getElementsByClassName(divId)[0];
        div.scrollTop = 0;
      }

      render() {
        return (
          <div id={divId}>
            <BaseComponent
              {...this.props}
              scrollToTop={this.scrollToTop}
            />
          </div>
        );
      }
    }
  );
  return innerWithDivScrollToTop;
}

export default WithDivScrollToTop;
```

## 使用

```js
import React, { Component } from 'react';

class AComponent extends Component {

  aFunction = () => {
    this.props.scrollToTop();
  }

  // ...
}

export default WithDivScrollToTop(AComponent);
```

