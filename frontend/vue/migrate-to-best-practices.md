## 使用 Setup 语法

你应该使用 Vue 3 的 Setup 语法来编写 Vue 组件，即 `<script setup lang="ts">`。这已经是 Vue 3 的现代通用标准。

## 使用 TypeScript 版本的 props/emit 声明

props 和 emit 也可以通过给 defineProps 和 defineEmits 传递纯类型参数的方式来声明：

```typescript
const props = defineProps<{
  foo: string
  bar?: number
}>()

const emit = defineEmits<{
  (e: 'change', id: number): void
  (e: 'update', value: string): void
}>()

// 3.3+：另一种更简洁的语法
const emit = defineEmits<{
  change: [id: number] // 具名元组语法
  update: [value: string]
}>()
```

- defineProps 或 defineEmits 要么使用运行时声明，要么使用类型声明。同时使用两种声明方式会导致编译报错。

## 使用 defineModel()

这个宏可以用来声明一个双向绑定 prop，通过父组件的 v-model 来使用。组件 v-model 指南中也讨论了示例用法。

在底层，这个宏声明了一个 model prop 和一个相应的值更新事件。如果第一个参数是一个字符串字面量，它将被用作 prop 名称；否则，prop 名称将默认为 "modelValue"。在这两种情况下，你都可以再传递一个额外的对象，它可以包含 prop 的选项和 model ref 的值转换选项。

```typescript
// 声明 "modelValue" prop，由父组件通过 v-model 使用
const model = defineModel<string>()
// 在被修改时，触发 "update:modelValue" 事件
model.value = "hello"

// 声明 "count" prop，由父组件通过 v-model:count 使用
const count = defineModel<number>("count")

function inc() {
  // 在被修改时，触发 "update:count" 事件
  count.value++
}
```

与 defineProps 和 defineEmits 一样，defineModel 也可以接收类型参数来指定 model 值和修饰符的类型：

```typescript
const modelValue = defineModel<string>()
//    ^? Ref<string | undefined>

// 用带有选项的默认 model，设置 required 去掉了可能的 undefined 值
const modelValue = defineModel<string>({ required: true })
//    ^? Ref<string>

const [modelValue, modifiers] = defineModel<string, "trim" | "uppercase">()
//                 ^? Record<'trim' | 'uppercase', true | undefined>
```

## 善于分割组件

如果一个 Page 甚至 Compose 很长，把他们分割成多个组件会是好主意，这能极大程度地增加可维护性。

在组件之间应该善用 Vue 的响应式最佳实践透传参数和状态，比如一个浮窗通常通过一个 v-model:show 来让外部控制是否展示。
