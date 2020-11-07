# cl-crud-export

依赖于 [`cl-crud`](https://www.npmjs.com/package/cl-crud?activeTab=readme)，可导出当页表格的数据为Excel

## Using npm

```shell
npm i cl-crud-export
```

## Props

| 参数           | 类型             | 说明                                    | 默认                         |
| -------------- | ---------------- | --------------------------------------- | ---------------------------- |
| filename       | Function, String | 导出文件名                              | yyyy-MM-dd HH_mm_ss          |
| autoWidth      | Boolean          | 自动调节宽度                            | true                         |
| bookType       | String           | 类型                                    | xlsx                         |
| header         | Array            | 表头                                    | 默认使用 table.column[label] |
| fields         | Array            | 字段                                    | 默认使用 table.column[prop]  |
| data           | Function, Array  | 数据                                    | 默认使用 page 获取数据       |
| maxExportLimit | Number           | 最大导出条数，不传或者小于等于0为不限制 | 0                            |
| size           | String           | 按钮尺寸                                | mini                         |
| disabled       | Boolean          | 是否禁用                                | false                        |
| type           | String           | 按钮类型                                | default                      |
| plain          | Boolean          | 按钮镂空                                | false                        |
| round          | Boolean          | 按钮圆角                                | false                        |
| circle         | Boolean          | 按钮圆形                                | false                        |
| icon           | String           | 按钮图标                                | null                         |


## Example

```js
// 注入 

import Vue from 'vue'
import crud from 'cl-crud'
import ExportBtn from 'cl-crud-export'

Vue.use(crud, {
	components: {
		ExportBtn
	}
})
```

```js
// 使用

// 方式一 
ctx.set('layout', [
	[..., 'export-btn'],
	...
])

// 方式二
ctx.set('layout', [
	[..., ({h}) => {
		return h('export-btn', {
			props: {
				maxExportLimit: 100
			}
		})
	}],
	...
])

// 方式三
ctx.set('layout', [
	[..., <export-btn maxExportLimit={100}>],
	...
])
```