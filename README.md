# BreezeSelect

This is a vue select component.

## Install

Use npm to download code:


```
npm install breeze-vue-select --save
```

import it into your project.

```js
import BreezeSelectTemplate from 'breeze-vue-select'
```

## Usage

```js
import BreezeSelectTemplate from 'breeze-vue-select'
export default {
  data() {
    return {
      data: [
        {
          text: '选项1',
          value: 1
        }, {
          text: '选项2',
          value: 2
        }
      ]
    }
  },
  methods: {
    change(value) {
      console.log(value)
    }
  },
  components: {
    BreezeSelectTemplate
  }
}
```

## Props

|        Prop       |   Type   | Default  |             Description             |
|-------------------|----------|----------|-------------------------------------|
| data              | Array    | 空       | drop-drown box option data          |
| width             | Number   | 120      | drop-drown box width                |
| placeholder       | String   | 请选择   | drop-drown box placeholder          |
| placeholderSearch | String   | 查找内容 | drop-drown box placeholder          |
| selectAllText     | String   | 空       | full selection button text          |
| search            | Boolean  | false    | whether the search box is displayed |
| multiple          | Boolean  | false    | whether multi selection             |
| callbackChange    | Function | 空函数   | change callback function            |

## License

[MIT](https://github.com/breeze55/breeze-vue-select/blob/master/LICENSE)




