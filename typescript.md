1. 基础类型
```typescript
let str: string = 'hello'
let num: number = 123
let bool: boolean = true
let arr: number[] = [1, 2, 3]
let arr2: Array<number> = [1, 2, 3]
let tuple: [string, number] = ['age', 18]
let obj: object = {}
let un: undefined = undefined
let nul: null = null
let any: any = '任意类型'
let unknown: unknown = '未知类型'
let never: never
let voidFunc: void = undefined
```

2. 联合类型和交叉类型
```typescript
type ID = string | number
let id: ID = 123

type A = { name: string }
type B = { age: number }
type C = A & B
let person: C = { name: '张三', age: 18 }
```

3. 接口和类型别名
```typescript
interface User {
  name: string
  age: number
  email?: string
  readonly id: number
}

type Person = {
  name: string
  age: number
}

interface 可以声明合并，type不可以
interface User {
  phone: string
}
```

4. 函数类型
```typescript
function add(a: number, b: number): number {
  return a + b
}

const multiply = (a: number, b: number): number => a * b

type CalcFunc = (a: number, b: number) => number
const subtract: CalcFunc = (a, b) => a - b

function greet(name: string, age?: number): string {
  return `Hello ${name}`
}

function sum(...nums: number[]): number {
  return nums.reduce((a, b) => a + b, 0)
}
```

5. 泛型
```typescript
function identity<T>(arg: T): T {
  return arg
}

function getArray<T>(items: T[]): T[] {
  return items
}

interface Response<T> {
  code: number
  data: T
  message: string
}

type UserResponse = Response<User>

function getFirstItem<T>(arr: T[]): T {
  return arr[0]
}

const num = getFirstItem([1, 2, 3])
const str = getFirstItem(['a', 'b'])
```

6. 类
```typescript
class Person {
  name: string
  age: number
  private password: string
  protected email: string
  readonly id: number

  constructor(name: string, age: number) {
    this.name = name
    this.age = age
    this.id = Date.now()
  }

  sayHello(): string {
    return `Hello, ${this.name}`
  }
}

class Student extends Person {
  grade: number

  constructor(name: string, age: number, grade: number) {
    super(name, age)
    this.grade = grade
  }
}

abstract class Animal {
  abstract makeSound(): void
  
  move(): void {
    console.log('moving')
  }
}

class Dog extends Animal {
  makeSound(): void {
    console.log('Woof!')
  }
}
```

7. 枚举
```typescript
enum Direction {
  Up,
  Down,
  Left,
  Right
}

const dir: Direction = Direction.Up

enum Status {
  Pending = 0,
  Success = 1,
  Failed = 2
}

enum Color {
  Red = 'red',
  Green = 'green',
  Blue = 'blue'
}

const enum Size {
  Small = 1,
  Medium = 2,
  Large = 3
}
```

8. 类型断言
```typescript
const value: any = 'hello'
const length1 = (value as string).length
const length2 = (<string>value).length

const el = document.getElementById('app') as HTMLDivElement

const data = JSON.parse(str) as User

const el2 = document.getElementById('app')!
el2.style.color = 'red'
```

9. 类型守卫
```typescript
function isString(val: unknown): val is string {
  return typeof val === 'string'
}

function isNumber(val: unknown): val is number {
  return typeof val === 'number'
}

function process(val: string | number) {
  if (isString(val)) {
    return val.toUpperCase()
  }
  if (isNumber(val)) {
    return val.toFixed(2)
  }
}

interface Fish {
  swim: () => void
}

interface Bird {
  fly: () => void
}

function isFish(pet: Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined
}

function move(pet: Fish | Bird) {
  if (isFish(pet)) {
    pet.swim()
  } else {
    pet.fly()
  }
}
```

10. 工具类型
```typescript
interface User {
  id: number
  name: string
  age: number
  email: string
  password: string
}

type PartialUser = Partial<User>
const user1: PartialUser = { name: '张三' }

type RequiredUser = Required<User>

type ReadonlyUser = Readonly<User>

type PickUser = Pick<User, 'id' | 'name'>
const user2: PickUser = { id: 1, name: '张三' }

type OmitUser = Omit<User, 'password'>
const user3: OmitUser = { id: 1, name: '张三', age: 18, email: '' }

type RecordType = Record<string, User>
const users: RecordType = {
  'user1': { id: 1, name: '张三', age: 18, email: '', password: '' }
}

type UserKeys = keyof User

type UserValues = User[keyof User]

type NonNullableType = NonNullable<string | null | undefined>

type ReturnTypeFunc = ReturnType<() => string>

type ParametersFunc = Parameters<(a: number, b: string) => void>
```

11. 索引类型和映射类型
```typescript
interface Person {
  name: string
  age: number
}

type PersonKeys = keyof Person

function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key]
}

const person = { name: '张三', age: 18 }
const name = getProperty(person, 'name')

type Readonly<T> = {
  readonly [P in keyof T]: T[P]
}

type Partial<T> = {
  [P in keyof T]?: T[P]
}

type Pick<T, K extends keyof T> = {
  [P in K]: T[P]
}
```

12. 条件类型
```typescript
type IsString<T> = T extends string ? true : false

type A = IsString<string>
type B = IsString<number>

type NonNullable<T> = T extends null | undefined ? never : T

type TypeName<T> =
  T extends string ? 'string' :
  T extends number ? 'number' :
  T extends boolean ? 'boolean' :
  T extends undefined ? 'undefined' :
  T extends Function ? 'function' :
  'object'

type T1 = TypeName<string>
type T2 = TypeName<123>
```

13. infer 关键字
```typescript
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never

type Func = () => string
type FuncReturn = ReturnType<Func>

type Parameters<T> = T extends (...args: infer P) => any ? P : never

type FuncParams = Parameters<(a: number, b: string) => void>

type Unpacked<T> = T extends (infer U)[] ? U : T

type T1 = Unpacked<string[]>
type T2 = Unpacked<number>
```

14. 字面量类型
```typescript
type Direction = 'left' | 'right' | 'up' | 'down'
let dir: Direction = 'left'

type Status = 'success' | 'error' | 'loading'
let status: Status = 'success'

type RequestMethod = 'GET' | 'POST' | 'PUT' | 'DELETE'

type ButtonSize = 'small' | 'medium' | 'large'

const config = {
  method: 'GET' as const,
  url: '/api/user'
}
```

15. 模块声明
```typescript
declare module 'my-lib' {
  export function myFunction(): void
  export const myValue: number
}

declare global {
  interface Window {
    myCustomProperty: string
  }
}

window.myCustomProperty = 'hello'

declare namespace MyNamespace {
  interface Config {
    apiUrl: string
  }
  
  function init(config: Config): void
}
```
