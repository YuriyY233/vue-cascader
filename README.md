##### 背景

因为之前维护老项目采用的技术栈是vue1，采用的ui组件是vue-beauty比较简单的ui组件，因为当时还没有element-ui这种比较好的ui组件，但是由于项目要求，需要用到多级多选的级联框，所以就决定用原生编写一个比较简陋的cascader组件

##### 用法

```html
<Cascader :list="list" @onClick="handleClick"></Cascader>
在vue项目中,先引入该组件，再注册component
import Cascaser from 'components/Cascader.vue'
{
	data(){
 		return{}
	},
	components:{
    	'Cascader':Cascader
	}
}
```

```
list数据格式：
list: [{name: "粤菜",
        value: 1,
        children: [{
           name: "清蒸桂花鱼",
           value: 11
           },{
           name: "梅菜扣肉",
           value: 12
           }]
        },{
         name: "湘菜",
         value: 2,
         children: [{
           name: "小炒肉",
           value: 21
            },{
              name: "剁椒鱼头",
              value: 22
               },{
              name: "水煮鱼",
              value: 23
               }]
          },{
             name: "甜品",
             value: 3,
             children: [{
               name: "西米露",
               value: 31
               },{
                name: "双皮奶",
                value: 32
                },{
                name: "凉粉",
                value: 33
                }]
           }
          ]
```

##### 事件

```javascript
onClick
默认回调值为数组，即返回value值，[1,11]代表选中两级的value值，只有一个值时表示选中一级的value值：
[[1,11],2] //表示选中：粤菜/清蒸桂花鱼，湘菜
```

