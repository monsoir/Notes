# 在 Chrome 中调试 Redux

1. 在 Chrome 中安装 Redux Devtools 插件
2. 在创建 Store 时使用中间件的形式将插件激活

    ```js
    // index.js
    // 这里结合 Redux 异步使用
    
    // 需要引入 compose
    import { createStore, applyMiddleware, compose } from 'redux';
    
    // 判断浏览器是否已经安装了 Redux Devtools 插件
    const reduxDevTools = window.devToolsExtension() || (() => {})();
    
    // 创建 Store, 将 thunk 与 reduxDevTools 在 compose 中合成
    const store = createStore(aReducer, compose(
    applyMiddleware(thunk),
    reduxDevTools,
    ));
    ```

3. 在 Chrome 中打开开发者工具，选中 Redux 选项卡


