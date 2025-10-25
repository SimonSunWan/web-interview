1. Node.js 基础概念
* JavaScript 运行时环境，基于 V8 引擎
* 事件驱动、非阻塞 I/O 模型
* 单线程，但通过事件循环实现高并发
* 适用于 I/O 密集型应用，不适合 CPU 密集型

2. 模块系统
```javascript
const fs = require('fs')
const path = require('path')

module.exports = { add, subtract }

exports.multiply = (a, b) => a * b

const { add } = require('./math')

import fs from 'fs'
export default function() {}
export { add, subtract }
```

3. CommonJS vs ES Module
* CommonJS：require/module.exports，同步加载，运行时加载
* ES Module：import/export，异步加载，编译时加载
* CommonJS 值拷贝，ES Module 值引用
* Node.js 通过 .mjs 或 package.json type: "module" 支持 ESM

4. 事件循环
```
   ┌───────────────────────────┐
┌─>│           timers          │ setTimeout/setInterval
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │     pending callbacks     │ 系统操作回调
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │       idle, prepare       │ 内部使用
│  └─────────────┬─────────────┘      ┌───────────────┐
│  ┌─────────────┴─────────────┐      │   incoming:   │
│  │           poll            │<─────┤  connections, │
│  └─────────────┬─────────────┘      │   data, etc.  │
│  ┌─────────────┴─────────────┐      └───────────────┘
│  │           check           │ setImmediate
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
└──┤      close callbacks      │ socket.on('close')
   └───────────────────────────┘
```

5. 事件循环执行顺序
* timers：执行 setTimeout 和 setInterval 回调
* pending callbacks：执行延迟到下一个循环迭代的 I/O 回调
* poll：检索新的 I/O 事件，执行 I/O 回调
* check：执行 setImmediate 回调
* close callbacks：执行关闭回调
* process.nextTick 优先级最高，在每个阶段结束后立即执行
* Promise.then 等微任务在 nextTick 之后执行

6. Buffer
```javascript
const buf1 = Buffer.from('hello')
const buf2 = Buffer.alloc(10)
const buf3 = Buffer.allocUnsafe(10)

buf1.toString()
buf1.toString('base64')

Buffer.concat([buf1, buf2])
Buffer.isBuffer(buf1)

buf1.length
buf1.slice(0, 2)
```

7. Stream 流
* Readable：可读流，fs.createReadStream
* Writable：可写流，fs.createWriteStream
* Duplex：双工流，net.Socket
* Transform：转换流，zlib.createGzip

```javascript
const fs = require('fs')

const readStream = fs.createReadStream('input.txt')
const writeStream = fs.createWriteStream('output.txt')

readStream.pipe(writeStream)

readStream.on('data', chunk => {
  console.log(chunk)
})

readStream.on('end', () => {
  console.log('读取完成')
})

readStream.on('error', err => {
  console.error(err)
})
```

8. fs 文件系统
```javascript
const fs = require('fs')
const fsPromises = require('fs').promises

fs.readFile('file.txt', 'utf8', (err, data) => {
  if (err) throw err
  console.log(data)
})

const data = fs.readFileSync('file.txt', 'utf8')

await fsPromises.readFile('file.txt', 'utf8')

fs.writeFile('file.txt', 'content', err => {})

fs.appendFile('file.txt', 'content', err => {})

fs.unlink('file.txt', err => {})

fs.mkdir('dir', { recursive: true }, err => {})

fs.readdir('dir', (err, files) => {})

fs.stat('file.txt', (err, stats) => {
  stats.isFile()
  stats.isDirectory()
  stats.size
})
```

9. path 路径模块
```javascript
const path = require('path')

path.join('/a', 'b', 'c')

path.resolve('a', 'b', 'c')

path.basename('/a/b/c.txt')

path.dirname('/a/b/c.txt')

path.extname('/a/b/c.txt')

path.parse('/a/b/c.txt')

path.format({ dir: '/a/b', name: 'c', ext: '.txt' })

path.isAbsolute('/a/b')

path.relative('/a/b', '/a/c')

__dirname
__filename
```

10. http 模块
```javascript
const http = require('http')

const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' })
  res.end('Hello World')
})

server.listen(3000)

http.get('http://example.com', res => {
  let data = ''
  res.on('data', chunk => data += chunk)
  res.on('end', () => console.log(data))
})

const postData = JSON.stringify({ key: 'value' })
const options = {
  hostname: 'example.com',
  port: 80,
  path: '/api',
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Content-Length': Buffer.byteLength(postData)
  }
}

const req = http.request(options, res => {
  res.on('data', chunk => console.log(chunk.toString()))
})
req.write(postData)
req.end()
```

11. events 事件模块
```javascript
const EventEmitter = require('events')

class MyEmitter extends EventEmitter {}

const emitter = new MyEmitter()

emitter.on('event', (arg1, arg2) => {
  console.log(arg1, arg2)
})

emitter.once('event', () => {
  console.log('只执行一次')
})

emitter.emit('event', 'a', 'b')

emitter.removeListener('event', listener)

emitter.removeAllListeners('event')

emitter.listenerCount('event')

emitter.listeners('event')
```

12. process 进程对象
```javascript
process.argv

process.env.NODE_ENV

process.cwd()

process.exit(0)

process.nextTick(callback)

process.pid

process.platform

process.version

process.memoryUsage()

process.cpuUsage()

process.on('uncaughtException', err => {
  console.error(err)
  process.exit(1)
})

process.on('unhandledRejection', (reason, promise) => {
  console.error(reason)
})

process.on('SIGINT', () => {
  console.log('退出')
  process.exit(0)
})
```

13. child_process 子进程
```javascript
const { exec, execFile, spawn, fork } = require('child_process')

exec('ls -la', (error, stdout, stderr) => {
  if (error) console.error(error)
  console.log(stdout)
})

execFile('node', ['--version'], (error, stdout, stderr) => {
  console.log(stdout)
})

const child = spawn('ls', ['-la'])
child.stdout.on('data', data => console.log(data.toString()))
child.stderr.on('data', data => console.error(data.toString()))
child.on('close', code => console.log(`退出码: ${code}`))

const forked = fork('child.js')
forked.send({ msg: 'hello' })
forked.on('message', msg => console.log(msg))
```

14. cluster 集群
```javascript
const cluster = require('cluster')
const http = require('http')
const numCPUs = require('os').cpus().length

if (cluster.isMaster) {
  console.log(`主进程 ${process.pid} 正在运行`)

  for (let i = 0; i < numCPUs; i++) {
    cluster.fork()
  }

  cluster.on('exit', (worker, code, signal) => {
    console.log(`工作进程 ${worker.process.pid} 已退出`)
    cluster.fork()
  })
} else {
  http.createServer((req, res) => {
    res.writeHead(200)
    res.end('Hello World')
  }).listen(8000)

  console.log(`工作进程 ${process.pid} 已启动`)
}
```

15. util 工具模块
```javascript
const util = require('util')

const sleep = util.promisify(setTimeout)

await sleep(1000)

const fs = require('fs')
const readFile = util.promisify(fs.readFile)
const data = await readFile('file.txt', 'utf8')

util.inspect(obj, { depth: null, colors: true })

util.format('%s:%s', 'foo', 'bar')

util.types.isPromise(value)
util.types.isAsyncFunction(value)
```

16. crypto 加密模块
```javascript
const crypto = require('crypto')

const hash = crypto.createHash('sha256')
hash.update('some data')
const hex = hash.digest('hex')

const hmac = crypto.createHmac('sha256', 'secret')
hmac.update('data')
const result = hmac.digest('hex')

const randomBytes = crypto.randomBytes(16).toString('hex')

crypto.randomUUID()

const encrypted = crypto.publicEncrypt(publicKey, buffer)
const decrypted = crypto.privateDecrypt(privateKey, encrypted)
```

17. os 操作系统模块
```javascript
const os = require('os')

os.platform()
os.arch()
os.cpus()
os.totalmem()
os.freemem()
os.hostname()
os.homedir()
os.tmpdir()
os.uptime()
os.loadavg()
os.networkInterfaces()
```

18. 中间件机制
```javascript
class Middleware {
  constructor() {
    this.middlewares = []
  }

  use(fn) {
    this.middlewares.push(fn)
    return this
  }

  async run(context) {
    const dispatch = async (i) => {
      if (i === this.middlewares.length) return
      const fn = this.middlewares[i]
      await fn(context, () => dispatch(i + 1))
    }
    await dispatch(0)
  }
}

const app = new Middleware()

app.use(async (ctx, next) => {
  console.log(1)
  await next()
  console.log(2)
})

app.use(async (ctx, next) => {
  console.log(3)
  await next()
  console.log(4)
})

app.run({})
```

19. 错误处理
```javascript
try {
  throw new Error('错误')
} catch (err) {
  console.error(err.message)
  console.error(err.stack)
}

process.on('uncaughtException', (err) => {
  console.error('未捕获的异常:', err)
  process.exit(1)
})

process.on('unhandledRejection', (reason, promise) => {
  console.error('未处理的 Promise 拒绝:', reason)
})

const domain = require('domain')
const d = domain.create()
d.on('error', (err) => {
  console.error('捕获的错误:', err)
})
d.run(() => {
  throw new Error('错误')
})
```

20. 性能优化
* 使用 cluster 多进程
* 使用 stream 处理大文件
* 使用缓存减少计算
* 使用连接池管理数据库连接
* 避免同步操作阻塞事件循环
* 使用 worker_threads 处理 CPU 密集型任务
* 启用 gzip 压缩
* 使用 CDN 加速静态资源
* 使用 pm2 进程管理
* 监控内存泄漏

21. Express 框架
```javascript
const express = require('express')
const app = express()

app.use(express.json())
app.use(express.urlencoded({ extended: true }))
app.use(express.static('public'))

app.get('/api/users', (req, res) => {
  res.json({ users: [] })
})

app.post('/api/users', (req, res) => {
  const user = req.body
  res.status(201).json(user)
})

app.put('/api/users/:id', (req, res) => {
  const { id } = req.params
  const user = req.body
  res.json(user)
})

app.delete('/api/users/:id', (req, res) => {
  const { id } = req.params
  res.status(204).send()
})

app.use((err, req, res, next) => {
  console.error(err.stack)
  res.status(500).json({ error: err.message })
})

app.listen(3000)
```

22. 环境变量
```javascript
process.env.NODE_ENV

require('dotenv').config()

const port = process.env.PORT || 3000

const dbUrl = process.env.DATABASE_URL

const isProduction = process.env.NODE_ENV === 'production'

const isDevelopment = process.env.NODE_ENV === 'development'
```

23. package.json 常用字段
```json
{
  "name": "my-app",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js",
    "test": "jest"
  },
  "dependencies": {},
  "devDependencies": {},
  "engines": {
    "node": ">=14.0.0"
  },
  "type": "module"
}
```

24. npm 命令
```bash
npm init
npm install
npm install express
npm install --save-dev nodemon
npm uninstall express
npm update
npm outdated
npm list
npm run dev
npm publish
npm version patch
npm cache clean --force
```

25. 常用第三方模块
* express - Web 框架
* koa - Web 框架
* axios - HTTP 客户端
* nodemon - 自动重启
* dotenv - 环境变量
* joi - 数据验证
* bcrypt - 密码加密
* jsonwebtoken - JWT 认证
* mongoose - MongoDB ODM
* sequelize - SQL ORM
* redis - Redis 客户端
* socket.io - WebSocket
* pm2 - 进程管理
* winston - 日志
* multer - 文件上传
