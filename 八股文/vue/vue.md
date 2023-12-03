- **vue的组件通信方式**

1. props属性和emit自定义事件:

```js
// 子组件
{
  props: {
    name: { type: String, required: true },
    // 引用类型默认值需要用工厂函数返回	
    info: { type: Object,  required: false, default: () => ({})}
  },
  methods: {
    changePeople() {
      this.$emit('updatePeopleInfo', {})
    }
  }
}
// 父组件
<template>
  <PeopleInfo name="张三"，:info={sex: 1, age: 12} @updatePeopleInfo="handleInfoChange"></PeopleInfo>
</template>	

{
  methoda: {
    handleInfoChange(info) {}
  }
}
```

```js
// 子组件
import { defineProps, withDefaults, defineEmits } from 'vue';
interface IPeopleInfo {
    sex: 0 | 1
    hoppy?: string[]
}
const props = withDefaults(defineProps<IPeopleInfo>, {
  sex: 1,
  hoppy: ['play']
})

const emits = defineEmits(['updatePeopleInfo'])
const changePeople = () => {
  emits('updatePeopleInfo', {})
}

// 父组件
<template>
  <PeopleInfo :sex="1"，:hobby="[]" @updatePeopleInfo="handleInfoChange"></PeopleInfo>
</template>	
```

2. Event Bus(事件总线)：事件总线是一个用于在应用程序中传递事件的中央实例。可以创建一个全局的事件总线对象，然后在组件中使用它来触发和监听事件。组件可以通过事件总线进行通信，而不需要明确地引用彼此

```js
// vue2事件总线是基于发布订阅模式
Vue.prototype.$bus=new Vue()
// 父组件
{
  mounted() {
    this.$bus.$on('peopleInfoChange', (info) => {
      this.info = info
    })
  },
  beforeDestory() {
    this.$bus.$off('peopleInfoChange')
  }
}

// 子组件
{
  methods: {
    handlePeopleChange(info) {
      this.$bus.$emit('peopleInfoChange', info)
    }
  }
}

// 因为vue3取消了prototype属性，所以无法使用,可通过mitt三方库来使用
```

3. $refs

```js
// 子组件
{
  methods: {
    setPeople(info) {}
  }
}
// 父组件
<template>
  <PeopleInfo ref="peopleInfoRef"></PeopleInfo>
</template>	
{
  methods: {
    handleSetPeopleInfo() {
      this.$refs.setPeople({})
    }
  }
}
```

```js
// 子组件
import { defineExpose } from 'vue';
const setPeople = (info) => {}

defineExpose({
  setPeople
})

// 父组件
<template>
  <PeopleInfo ref="peopleInfoRef"></PeopleInfo>
</template>	

const peopleInfoRef = ref(null)
peopleInfoRef.value.setPeople()
```

4. vuex
5. provide和inject

```js
// 一般用于多层级的数据传递,不是相应的
// 父组件
{
  provide: {
    name: '张三'
  }
}
// 子组件
{
  inject: ['name']
}
```

```
// 父组件
import { provide } from 'vue
provide('name', '张三')

// 子组件
import { inject } from 'vue'
inject('name')
```