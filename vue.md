1. 响应式底层原理proxy
* proxy拦截对象 set get
* track 记下谁在用我
* trigger 通知用我的地方重新执行

2. 副作用清理
* watchEffect(onInvalidate => { ... }) 接收清理函数参数
* Vue自动清理effect本身，但不会清理我们创建的副作用
* onInvalidate(() => clearInterval(id)) 手动清理异步副作用
* 依赖变化或组件卸载时自动执行清理
* 条件清理：if (withCleanup.value) { onInvalidate(...) }
* 需要手动清理：setInterval、addEventListener、fetch请求等

3. vue的响应式更新
* 基于Proxy的响应式系统
* 依赖收集：track() 记录依赖关系
* 触发更新：trigger() 通知所有依赖
* 批量更新：将DOM更新放入异步队列，nextTick()统一执行
* 更新策略：同步更新数据，异步更新视图
* 优化：避免不必要的重新渲染

4. v-model怎么理解
* 语法糖：v-model = :value + @input
* 双向绑定：数据变化自动更新视图，视图变化自动更新数据
* 自定义组件：通过props接收value，通过emit发送input事件
* 修饰符：.lazy、.number、.trim等

5. vue通信
* prop emit
* provide inject
* eventBus
* pinia

6. 插槽
* 默认插槽
* 具名插槽
* 作用域插槽

7. 自定义指令
```
app.directive('color', {
    mounted(el, binding) {
        el.style.color = binding.value
    },
    updated(el, binding) {
        el.style.color = binding.value
    }
})
```

8. 服务端渲染
核心目录：
```
src/
  ├── entry-client.js    (客户端入口)
  ├── entry-server.js    (服务端入口)
  └── App.vue
server.js                (Node服务器)
```

- 只渲染初始状态，不执行客户端交互
- 需要配合 hydrate 进行客户端激活
- 服务端和客户端代码需要同构

核心实现：
```javascript
import { renderToString } from 'vue/server-renderer'
import { createApp } from 'vue'

const app = createApp({
  template: '<div>{{ message }}</div>',
  data() {
    return { message: 'Hello SSR!' }
  }
})

const html = await renderToString(app)
// html = '<div>Hello SSR!</div>'
```

9. Vite 项目配置与优化
核心配置项：
```javascript
// vite.config.ts
export default defineConfig(({ mode }) => {
  const isProd = mode === 'production'
  
  return {
    // 1. 插件配置
    plugins: [
      vue(),
      !isProd && vueDevTools()  // 开发环境启用 DevTools
    ].filter(Boolean),
    
    // 2. 路径别名
    resolve: {
      alias: {
        '@': fileURLToPath(new URL('./src', import.meta.url))
      }
    },
    
    // 3. 构建优化
    build: {
      target: 'es2015',           // 编译目标
      minify: 'esbuild',          // 使用 esbuild 压缩
      cssCodeSplit: true,         // CSS 代码分割
      sourcemap: !isProd,         // 生产环境禁用 sourcemap
      
      rollupOptions: {
        output: {
          // 文件输出配置（带 hash 利于缓存）
          chunkFileNames: 'js/[name]-[hash].js',
          entryFileNames: 'js/[name]-[hash].js',
          
          // 资源分类打包
          assetFileNames: (assetInfo) => {
            if (/\.(png|jpg|gif|svg)$/i.test(assetInfo.names[0])) {
              return 'images/[name]-[hash][extname]'
            }
            if (/\.css$/i.test(assetInfo.names[0])) {
              return 'css/[name]-[hash][extname]'
            }
            return 'assets/[name]-[hash][extname]'
          },
          
          // 代码分割策略
          manualChunks(id) {
            if (id.includes('node_modules')) {
              // Vue 核心库单独打包（利于长期缓存）
              if (id.includes('vue') || id.includes('pinia') || id.includes('vue-router')) {
                return 'vendor-vue'
              }
              return 'vendor'  // 其他第三方库
            }
          }
        }
      }
    },
    
    // 4. 开发服务器
    server: {
      port: 3000,
      open: true,
      cors: true,
      
      // 代理配置（解决跨域）
      proxy: {
        '/api': {
          target: 'http://localhost:8080',
          changeOrigin: true,
          rewrite: (path) => path.replace(/^\/api/, '')
        }
      }
    },
    
    // 5. ESBuild 优化
    esbuild: {
      pure: isProd ? ['console.log'] : [],      // 移除 console.log
      drop: isProd ? ['console', 'debugger'] : [] // 移除 console 和 debugger
    }
  }
})
```

```
function decideOptimization(type){
  switch(type){
    case '首屏慢': return 'SSR/预渲染+路由与组件懒加载+代码分割+关键资源预加载';
    case '交互卡': return '虚拟列表+节流防抖+减少重渲染+v-memo/缓存计算';
    case '特定页慢': return '拆组件+按需加载第三方库+keep-alive/分页/服务端分页';
    case '网络慢': return 'CDN+Gzip/Brotli+图片WebP懒加载+HTTP/2/3+缓存头';
    default: return '先量化指标与分析瓶颈再对症优化';
  }
}
```

10. 前端权限管理
核心实现：
```javascript
const usePermission = () => {
  const userStore = useUserStore()
  
  const hasPermission = (permission) => {
    return userStore.permissions.includes(permission)
  }
  
  const hasRole = (role) => {
    return userStore.roles.includes(role)
  }
  
  return { hasPermission, hasRole }
}

const router = createRouter({
  routes: [
    {
      path: '/admin',
      meta: { roles: ['admin'] }
    }
  ]
})

router.beforeEach((to, from, next) => {
  const userStore = useUserStore()
  
  if (to.meta.roles) {
    if (userStore.roles.some(role => to.meta.roles.includes(role))) {
      next()
    } else {
      next('/403')
    }
  } else {
    next()
  }
})
```

权限指令：
```javascript
app.directive('permission', {
  mounted(el, binding) {
    const { value } = binding
    const userStore = useUserStore()
    
    if (!userStore.permissions.includes(value)) {
      el.parentNode?.removeChild(el)
    }
  }
})
```

实现要点：
* 路由守卫：基于 meta 字段校验角色/权限
* 动态路由：根据用户权限动态注册路由
* 按钮级权限：自定义指令控制按钮显示隐藏
* 菜单权限：根据权限列表过滤菜单树
* 接口权限：拦截器统一处理 token 和权限错误

11. 单点登录（SSO）
核心流程：
```javascript
const ssoLogin = async () => {
  const urlParams = new URLSearchParams(window.location.search)
  const ticket = urlParams.get('ticket')
  
  if (ticket) {
    try {
      const { data } = await axios.post('/api/sso/verify', { ticket })
      
      localStorage.setItem('token', data.token)
      
      const userStore = useUserStore()
      await userStore.getUserInfo()
      
      router.replace('/')
    } catch (error) {
      window.location.href = 'https://sso.example.com/login'
    }
  } else {
    window.location.href = `https://sso.example.com/login?redirect=${encodeURIComponent(window.location.href)}`
  }
}
```

请求拦截器：
```javascript
axios.interceptors.request.use(config => {
  const token = localStorage.getItem('token')
  if (token) {
    config.headers.Authorization = `Bearer ${token}`
  }
  return config
})

axios.interceptors.response.use(
  response => response,
  error => {
    if (error.response?.status === 401) {
      localStorage.removeItem('token')
      window.location.href = 'https://sso.example.com/login'
    }
    return Promise.reject(error)
  }
)
```

实现要点：
* URL 参数携带 ticket 凭证
* 后端验证 ticket 换取 token
* Token 存储及自动续期
* 退出登录清理所有子系统
* 跨域 Cookie 共享（需同域或配置 CORS）

12. WebSocket 实时通信
核心实现：
```javascript
class WebSocketClient {
  constructor(url) {
    this.url = url
    this.ws = null
    this.reconnectTimer = null
    this.heartbeatTimer = null
    this.listeners = new Map()
  }
  
  connect() {
    this.ws = new WebSocket(this.url)
    
    this.ws.onopen = () => {
      this.startHeartbeat()
    }
    
    this.ws.onmessage = (event) => {
      const data = JSON.parse(event.data)
      const listeners = this.listeners.get(data.type) || []
      listeners.forEach(callback => callback(data.payload))
    }
    
    this.ws.onclose = () => {
      this.stopHeartbeat()
      this.reconnect()
    }
    
    this.ws.onerror = () => {
      this.ws.close()
    }
  }
  
  on(type, callback) {
    if (!this.listeners.has(type)) {
      this.listeners.set(type, [])
    }
    this.listeners.get(type).push(callback)
  }
  
  send(type, payload) {
    if (this.ws?.readyState === WebSocket.OPEN) {
      this.ws.send(JSON.stringify({ type, payload }))
    }
  }
  
  startHeartbeat() {
    this.heartbeatTimer = setInterval(() => {
      this.send('heartbeat', {})
    }, 30000)
  }
  
  stopHeartbeat() {
    if (this.heartbeatTimer) {
      clearInterval(this.heartbeatTimer)
      this.heartbeatTimer = null
    }
  }
  
  reconnect() {
    this.reconnectTimer = setTimeout(() => {
      this.connect()
    }, 3000)
  }
  
  close() {
    this.stopHeartbeat()
    if (this.reconnectTimer) {
      clearTimeout(this.reconnectTimer)
    }
    this.ws?.close()
  }
}

const useWebSocket = () => {
  const wsClient = ref(null)
  
  onMounted(() => {
    wsClient.value = new WebSocketClient('ws://localhost:8080')
    wsClient.value.connect()
    
    wsClient.value.on('dataUpdate', (data) => {
      // 处理数据更新
    })
  })
  
  onUnmounted(() => {
    wsClient.value?.close()
  })
  
  return { wsClient }
}
```

实现要点：
* 心跳检测：30s 发送一次心跳保持连接
* 自动重连：断线后 3s 自动重连
* 事件订阅：支持多种消息类型订阅
* 组件卸载：及时关闭连接避免内存泄漏
* 错误处理：网络异常时优雅降级

13. ECharts 性能优化
核心优化：
```javascript
const chartOption = {
  animation: false,
  
  series: [{
    type: 'line',
    data: [],
    sampling: 'lttb',
    large: true,
    largeThreshold: 2000,
    progressive: 500,
    progressiveThreshold: 3000
  }]
}

const useChart = (chartRef) => {
  let chart = null
  
  const initChart = () => {
    chart = echarts.init(chartRef.value, null, {
      renderer: 'canvas',
      useDirtyRect: true
    })
  }
  
  const updateChart = (newData) => {
    chart.setOption(chartOption, {
      notMerge: false,
      lazyUpdate: true,
      silent: false
    })
  }
  
  const resizeChart = () => {
    chart?.resize()
  }
  
  onMounted(() => {
    initChart()
    window.addEventListener('resize', resizeChart)
  })
  
  onUnmounted(() => {
    window.removeEventListener('resize', resizeChart)
    chart?.dispose()
  })
  
  return { updateChart }
}
```

优化策略：
* 数据采样：使用 sampling: 'lttb' 降采样
* 渐进渲染：大数据量分批渲染
* 关闭动画：animation: false 提升渲染速度
* Canvas 渲染：大数据量使用 canvas 而非 SVG
* 脏矩形优化：useDirtyRect 只重绘变化区域
* 按需更新：lazyUpdate 延迟更新
* 及时销毁：dispose() 释放内存
* 节流防抖：resize 事件节流处理

14. 组件封装
通用表格组件：
```javascript
const TableWrapper = defineComponent({
  props: {
    columns: Array,
    api: Function,
    queryParams: Object
  },
  
  setup(props) {
    const tableData = ref([])
    const total = ref(0)
    const loading = ref(false)
    const pagination = reactive({
      page: 1,
      pageSize: 10
    })
    
    const fetchData = async () => {
      loading.value = true
      try {
        const { data } = await props.api({
          ...props.queryParams,
          ...pagination
        })
        tableData.value = data.list
        total.value = data.total
      } finally {
        loading.value = false
      }
    }
    
    const handlePageChange = (page) => {
      pagination.page = page
      fetchData()
    }
    
    watch(() => props.queryParams, () => {
      pagination.page = 1
      fetchData()
    }, { deep: true })
    
    onMounted(() => {
      fetchData()
    })
    
    return {
      tableData,
      total,
      loading,
      pagination,
      handlePageChange
    }
  }
})
```

封装原则：
* 单一职责：每个组件只做一件事
* 可配置性：通过 props 控制行为
* 插槽扩展：预留插槽支持自定义
* 事件透传：emit 关键事件给父组件
* 样式隔离：scoped 避免样式污染
* 类型定义：TypeScript 提供类型提示

15. 多步骤表单校验
核心实现：
```javascript
const useStepForm = () => {
  const currentStep = ref(0)
  const formRefs = ref([])
  
  const formData = reactive({
    step1: { name: '', phone: '' },
    step2: { idCard: '', address: '' },
    step3: { bankCard: '', bank: '' }
  })
  
  const rules = {
    step1: {
      name: [{ required: true, message: '请输入姓名' }],
      phone: [
        { required: true, message: '请输入手机号' },
        { pattern: /^1[3-9]\d{9}$/, message: '手机号格式错误' }
      ]
    },
    step2: {
      idCard: [
        { required: true, message: '请输入身份证号' },
        { pattern: /^\d{17}[\dXx]$/, message: '身份证号格式错误' }
      ]
    }
  }
  
  const nextStep = async () => {
    const formRef = formRefs.value[currentStep.value]
    try {
      await formRef.validate()
      currentStep.value++
    } catch (error) {
      return false
    }
  }
  
  const prevStep = () => {
    if (currentStep.value > 0) {
      currentStep.value--
    }
  }
  
  const submit = async () => {
    const formRef = formRefs.value[currentStep.value]
    try {
      await formRef.validate()
      const allData = { ...formData.step1, ...formData.step2, ...formData.step3 }
      await axios.post('/api/submit', allData)
    } catch (error) {
      // 处理错误
    }
  }
  
  return {
    currentStep,
    formData,
    rules,
    formRefs,
    nextStep,
    prevStep,
    submit
  }
}
```

实现要点：
* 分步校验：每步单独校验，通过后才能下一步
* 数据保留：切换步骤时保留已填数据
* 错误提示：实时校验并显示错误信息
* 自定义校验：支持异步校验（如手机号重复检测）
* 进度展示：显示当前步骤和已完成步骤

16. 首屏加载优化
优化策略：
```javascript
const routes = [
  {
    path: '/',
    component: () => import('./views/Home.vue')
  },
  {
    path: '/about',
    component: () => import('./views/About.vue')
  }
]

const preloadComponent = (component) => {
  return () => {
    const promise = component()
    promise.catch(() => {
      return import('./views/Error.vue')
    })
    return promise
  }
}

<script setup>
import { defineAsyncComponent } from 'vue'

const HeavyComponent = defineAsyncComponent({
  loader: () => import('./components/HeavyComponent.vue'),
  loadingComponent: LoadingSpinner,
  errorComponent: ErrorComponent,
  delay: 200,
  timeout: 3000
})
</script>
```

具体措施：
* 路由懒加载：使用动态 import
* 组件异步加载：defineAsyncComponent
* 图片懒加载：v-lazy 指令或 IntersectionObserver
* 代码分割：Vite manualChunks 配置
* 压缩资源：Gzip/Brotli 压缩
* CDN 加速：静态资源使用 CDN
* 预加载关键资源：<link rel="preload">
* 骨架屏：提升感知性能

17. 表格分页优化
虚拟滚动实现：
```javascript
const useVirtualScroll = (itemHeight, visibleCount) => {
  const scrollTop = ref(0)
  const allData = ref([])
  
  const startIndex = computed(() => {
    return Math.floor(scrollTop.value / itemHeight)
  })
  
  const endIndex = computed(() => {
    return startIndex.value + visibleCount
  })
  
  const visibleData = computed(() => {
    return allData.value.slice(startIndex.value, endIndex.value)
  })
  
  const totalHeight = computed(() => {
    return allData.value.length * itemHeight
  })
  
  const offsetY = computed(() => {
    return startIndex.value * itemHeight
  })
  
  const handleScroll = (e) => {
    scrollTop.value = e.target.scrollTop
  }
  
  return {
    visibleData,
    totalHeight,
    offsetY,
    handleScroll
  }
}
```

后端分页：
```javascript
const useTablePagination = () => {
  const tableData = ref([])
  const total = ref(0)
  const loading = ref(false)
  const page = ref(1)
  const pageSize = ref(20)
  
  const fetchData = async () => {
    loading.value = true
    try {
      const { data } = await axios.get('/api/list', {
        params: {
          page: page.value,
          pageSize: pageSize.value
        }
      })
      tableData.value = data.list
      total.value = data.total
    } finally {
      loading.value = false
    }
  }
  
  const handlePageChange = (newPage) => {
    page.value = newPage
    fetchData()
  }
  
  return {
    tableData,
    total,
    loading,
    page,
    pageSize,
    handlePageChange,
    fetchData
  }
}
```

优化策略：
* 虚拟滚动：只渲染可见区域的数据
* 后端分页：每次只加载一页数据
* 按需加载：滚动到底部加载更多
* 缓存策略：已加载的数据缓存起来
* 防抖优化：搜索输入防抖处理

18. 骨架屏
核心实现：
```javascript
<template>
  <div v-if="loading" class="skeleton">
    <div class="skeleton-header"></div>
    <div class="skeleton-body">
      <div class="skeleton-line" v-for="i in 5" :key="i"></div>
    </div>
  </div>
  <div v-else>
    <!-- 实际内容 -->
  </div>
</template>

<style scoped>
.skeleton-line {
  height: 20px;
  background: linear-gradient(90deg, #f0f0f0 25%, #e0e0e0 50%, #f0f0f0 75%);
  background-size: 200% 100%;
  animation: loading 1.5s infinite;
  border-radius: 4px;
  margin-bottom: 10px;
}

@keyframes loading {
  0% { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}
</style>
```

自动生成骨架屏：
```javascript
const SkeletonScreen = defineComponent({
  props: {
    rows: { type: Number, default: 5 },
    avatar: { type: Boolean, default: false },
    loading: { type: Boolean, default: true }
  },
  
  setup(props, { slots }) {
    return () => {
      if (!props.loading) {
        return slots.default?.()
      }
      
      return (
        <div class="skeleton">
          {props.avatar && <div class="skeleton-avatar"></div>}
          {Array.from({ length: props.rows }).map(() => (
            <div class="skeleton-line"></div>
          ))}
        </div>
      )
    }
  }
})
```

实现要点：
* CSS 动画：模拟加载效果
* 结构相似：骨架屏结构与真实内容一致
* 组件封装：可复用的骨架屏组件
* 条件渲染：loading 状态控制显示
* 提升体验：减少白屏时间，提升感知性能

19. UniApp 分包策略
核心配置：
```json
{
  "pages": [
    {
      "path": "pages/index/index"
    }
  ],
  "subPackages": [
    {
      "root": "packageA",
      "pages": [
        {
          "path": "pages/admin/index"
        }
      ]
    },
    {
      "root": "packageB",
      "pages": [
        {
          "path": "pages/user/index"
        }
      ]
    }
  ],
  "preloadRule": {
    "pages/index/index": {
      "network": "all",
      "packages": ["packageA"]
    }
  }
}
```

实现要点：
* 主包：放置公共资源和首页
* 分包：按功能模块或角色划分
* 预下载：提前加载常用分包
* 独立分包：无需主包即可运行
* 大小限制：主包 2M，分包 2M，总包 20M
* 路径访问：通过完整路径跳转分包页面

分包优化：
* 公共资源放主包
* 低频功能单独分包
* 图片资源使用 CDN
* 及时清理无用代码
* 合理使用预下载
