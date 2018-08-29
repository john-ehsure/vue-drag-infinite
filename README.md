# vue-drag-infinite

> 一个vue自由拖拽的插件，自由修改div块的宽高颜色等。。。

## installation
```js
# install
npm install --save vue-drag-infinite
```
## vueDragInfinite 引入方式
``` js
//全局引入
import Vue from 'vue'
import vueDragInfinite from 'vue-drag-infinite'

Vue.use(vueDragInfinite)
```
## 页面使用方式
```html
<vue-drag-infinite :json="json" :name="name" @save="saveJson"></vue-drag-infinite>

export default {
  data () {
    return {
		name:'默认标题文字',//默认标题文字
		json:[//默认div块的信息 为空的时候只能新增不能修改
			{
	         style:{
	           width:"150px",
	           height:"150px",
	           fontSize:"14px",
	           color:"#fff",
	           left:"50px",
	           backgroundColor:"#999"
	         },
	         text:'这是第一个div'
	        },
		],
    }
  },
  methods:{
  	saveJson(obj){
  		console.log(obj)
  	}
  }
}
```