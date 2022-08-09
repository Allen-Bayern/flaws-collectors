# Vue 2官方文档错题本

## 单向数据流

> All props form a one-way-down binding between the child property and the parent one: when the parent property updates, it will flow down to the child, but not the other way around.

意即所有数据都是**单向流动**的。在数据更新时由父组件传到子组件，而非相反。

当非常需要对父组件传来的`props`修改时，可以用这两种方式来达成该效果，而不必修改`props`本身：

方法一：在`data`中定义属性，其初值为`prop`。例：

```javascript
export default Vue.extend({
  props: {
    initialCounter: {
      type: Number,
      default: () => 0,
      required: true,
    },
  },
  data() {
    return {
      newCounter: this.initialCounter,
    };
  }
});
```

方法二：如果可能需要加工，可以用计算属性。例：

```javascript
export default Vue.extend({
  props: {
    initialCounter: {
      type: Number,
      default: () => 0,
    },
  },
  computed: {
    newCounter: function() {
      return this.initialCounter + 1;
    },
  },
});
```

## props中传引用类型时，默认值必须以工厂函数的形式给出！

引用：

> // Object or array defaults must be returned from a factory function

例：

```javascript
export default Vue.extend({
  props: {
    propArray: {
      type: Array,
      default() {
        return [];
      },
      // 如果是必传属性，则加一条required: true
      required: true,
    },
    propObject: {
      type: Object,
      // 箭头函数也行
      default: () => new Object(),
    },
    // 也可自定义validator
    propValidator: {
      validator: function(val) {
        return ['success', 'warning', 'danger'].includes(val);
      },
    },
    // 甚至你可以传Function
    propFunction: Function,
  },
});
```

## $emit自定义事件时尽量使用kebab-case为自定义事件命名

> Unlike components and props, event names will never be used as variable or property names in JavaScript, so **there’s no reason to use camelCase or PascalCase**. Additionally, v-on event listeners inside DOM templates will be automatically transformed to lowercase (due to HTML’s case-insensitivity), so v-on:myEvent would become v-on:myevent – making myEvent impossible to listen to.
> 
> For these reasons, we recommend you always use **kebab-case** for event names.

也就是说，小驼峰camelCase或者大驼峰（帕斯卡）PascalCase尽量不要用于命名`this.$emit('event-name')`中的`event-name`

## 一种半自动v-model写法

```javascript
export default {
  props: {
    value: {
      type: String,
    },
    computed: {
      name: {
        get() {
          return this.value;
        },
        set(value) {
          this.$emit('input', value);
        }
      },
    },
  },
};
```