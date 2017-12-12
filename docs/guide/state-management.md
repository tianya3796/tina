# 状态管理

在构建复杂应用时，随着页面和组件的增加，维护跨组件 (或页面) 状态的复杂度也会快速地增长。在 web 前端领域，解决该难题的常见思路是使用由 Facebook 提出的 [Flux 架构](https://facebook.github.io/flux/)；具体到 [React](https://reactjs.org/) 和 [Vue](https://vuejs.org) 中，则分别主流使用 [Redux](https://redux.js.org/) 和 [Vuex](https://vuex.vuejs.org/) 作为其 Flux 实现。

## Tinax
在 tina 框架中，我们推荐使用 [Tinax](https://github.com/tinajs/tinax) 作为你的状态管理器。

尽管 Tinax 的 API 与 Vuex 并不完全一致，但毫无疑问 Tinax 受到了 Vuex 的启发，并学习了 Vuex 的核心概念 —— ``state``, ``getter``, ``mutation``, ``action``, ``module`` 等；如果你还没有用过 Vuex，我们建议你先了解 [Vuex 的核心概念](https://vuex.vuejs.org/zh-cn/core-concepts.html)。

示例：

```javascript
/**
 * /store/session.js
 */

import types from '../types'
import { fetchSession } from '../../api'

const initialState = {
  expiredAt: 0,
}

const getters = {
  isExpired: (state) => state.expiredAt < Date.now(),
}

const actions = {
  fetchSession ({ commit, state }, { key }) {
    fetchSession(key).then((session) => commit(types.SET_SESSION, { session }))
  },
}

const mutations = {
  [types.SET_SESSION] (state, { session }) {
    return session
  },
}

export default {
  state: initialState,
  getters,
  actions,
  mutations,
}
```

```javascript
/**
 * /demo-page.js or <script> in /demo-page.mina
 */

import { Page } from '@tinajs/tina'
import { store } from './store'

Page.define({
  mixins: [
    store.connect({
      getters (getters) {
        return {
          isExpired: getters.isExpired(),
        }
      },
      actions ({ fetchSession }) {
        return {
          fetchSession,
        }
      },
    }),
  ],
  onLoad () {
    console.log(this.data.isExpired)
    this.fetchSession()
  },
})
```

如果你对 Tinax 感兴趣，欢迎访问 [@tinajs/tinax](https://github.com/tinajs/tinax) 了解更多信息。