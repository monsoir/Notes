# React 使用 Context

## 是什么

- 一个局部的“全局”值传递工具
- 避免组件间传值需要多次操作 `props`


## 怎么用

用法都是围绕生产者和消费者的关系，一个生产者可以对应多个消费者

1. 创建一个 Context
- 创建生产者 `Provider`
- 创建消费者 `Consumer`

以创建一个单选按钮(Radio)以及单选按钮管理组(RadioGroup)的组件为例，即 Radio 为 RadioGroup 的子组件时，Radio 只能选中其中一个

### 创建 Context

```jsx
import React from 'react';

const DefaultValue = {
  selection: {}, // 选中的项的数据
  onChange: () => {}, // 选中项发生变化
};
const context = React.createContext(DefaultValue);

export default context;

```

- 关键时使用 `React.createContext(...)` 来创建一个上下文对象
- `DefaultValue` 就是默认的上下文值

### 创建生产者 `Provider`

```jsx
import React from 'react';

import Context from 'path/to/Context';

const RadioGroup = ({ selection, onChange, ...props }) => (
  <Context.Provider value={{ selection, onChange }}>
    {props.children}
  </Context.Provider>
);

export default RadioGroup;

```

- 使用 `xxx.Provider` 来定义生产者
- `value` 就是上下文中的值，到时消费者 `Consumer` 拿到的就是里面的值

### 创建消费者 `Consumer`

> 组件使用 Ant Design 进行布局

```jsx
import React from 'react';
import { Radio, Card } from 'antd';

import Context from 'path/to/Context';

import styles from './GoodsCard.less';

const GoodsCard = props => (
  <Context.Consumer>
    {
      ({ selection, onChange }) => (
        // 24 为 Card 的一边 padding, 160 为图片的宽度
        <Card style={{ width: (160 + 2 * 24) }} bordered={false}>
          <div className={styles.imgArea}>
            <img src={props.image} alt="i" />
            {props.added ? (<div className={styles.memo}>已添加</div>) : null}
          </div>
          <div className={styles.content}>
            <span className={styles.title}>{props.title}</span>
            <span className={styles.useTimes}>{`添加次数：${props.useTimes}`}</span>
            <div className={styles.selectButton}>
              <Radio
                // 这里，我们可以直接拿到管理组中选中的值，而不需要通过 props 传递选中的值
                // props 就只需要传递单选按钮的值而已
                // 通过比较上下文中当前选中的值与当前单选按钮的值，来确定是否选中单选按钮
                checked={selection.value === props.value}
                onChange={() => { onChange(props); }}
              >
                添加
              </Radio>
            </div>
          </div>
        </Card>
      )
    }
  </Context.Consumer>
);

export default GoodsCard;

```

- 使用 `xxx.Consumer` 来定义消费者，可以在多个组件中使用消费者来获取到上下文中的值
- 通过代码块来获取到上下文中的值

    ```jsx
    <Context.Consumer>
      value => (
        // render component
        // value 就是上下文中的值，可以使用对象析构来获取到值
        // 像平时一样渲染组件
      )
    </Context.Consumer>
    ```

## References

- [Context](https://reactjs.org/docs/context.html)

