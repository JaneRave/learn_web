#### 1. Object.keys和some的结合用法

```js
<script type="text/javascript">
    var data= [{ 
                    date: '2015-05-02', 
                    name: '王小虎', 
                    address: '上海市普陀区金沙江路 1518 弄' 
                }, { 
                    date: '2017-05-04', 
                    name: '无浩然', 
                    address: '上海市普陀区金沙江路 1517 弄' 
                }, { 
                    date: '2017-05-04', 
                    name: '吴浩然', 
                    address: '上海市普陀区金沙江路 1517 弄' 
                }, { 
                    date: '2018-05-01', 
                    name: '小狮子', 
                    address: '上海市普陀区金沙江路 1519 弄' 
                }, { 
                    date: '2019-05-03', 
                    name: '大城西', 
                    address: '上海市普陀区金沙江路 1516 弄' 
                }];
    var a=data.filter(data1=>{
         return Object.keys(data1).some(key=>{
             return data1[key].indexOf('2017-05-04')>-1
             console.log(data1);
         })
    })
    console.log(a);
</script>
```

#### 2.树形结构处理

```
var json = '{"code":0,"parent":[{"name":"文件夹1","pid":0,"id":1},{"name":"文件夹2","pid":0,"id":2},{"name":"文件夹3","pid":0,"id":3},{"name":"文件夹1-1","pid":1,"id":4},{"name":"文件夹2-1","pid":2,"id":5}],"child":[{"name":"文件1","pid":1,"id":10001},{"name":"文件2","pid":1,"id":10002},{"name":"文件2-1","pid":2,"id":10003},{"name":"文件2-2","pid":2,"id":10004},{"name":"文件1-1-1","pid":4,"id":10005},{"name":"文件2-1-1","pid":5,"id":10006}]}';   
```

* 第一种方法

```
 	data = JSON.parse(json);
    var result = transformData(data);
    console.log(result);
    function transformData(data) {
        let allData = JSON.parse(JSON.stringify(data));
        var data_new = [...allData.parent,...allData.child];
        console.log(data_new);
    　　 var result = data_new.filter(parent=>{
    　　　　let findChildren = data_new.filter(child=>{
    　　　　　　return parent.id === child.pid
    　　　　})
            if(findChildren.length>0)  parent.children = findChildren;
    // 　　　　findChildren.length>0 ? parent.children = findChildren : parent.children = []
    　　　　return parent.pid == 0   //返回顶层，依据实际情况判断这里的返回值
    　　 })
        return result;
    }
```

* 第二种方法

```
 function transformData(data) {
     let allData = JSON.parse(JSON.stringify(data));
     var data_new = [...allData.parent,...allData.child];
     let treeMapList = data_new.reduce((obj, current) => {
         current.label = current.label || current.name;
         obj[current["id"]] = current;
         return obj;
     }, {});
     //看每个对象中pid值是否跟treeMapList中id的值相等，若相等就说是children中的数据
     let result = data_new.reduce((arr, current) => {
         let pid = current.pid;
         let parent = treeMapList[pid]; //拿父亲的数据
         if (parent) {
         parent.children
             ? parent.children.push(current)
             : (parent.children = [current]);
         } else if (pid === 0) {
         arr.push(current);
         }
         return arr;
     }, []);
     return result;
 }
```

#### 3. map从数组对象中返回需要的字段的简写

```
let arr2 = [
    {
        id: 1,
        name: "tony",
        class: 1,
        grade: 88
    },
    {
        id: 2,
        name: "Tom",
        class: 1,
        grade: 90
    },
    {
        id: 3,
        name: "Jack",
        class: 1,
        grade: 86
    }
];
let resu = arr2.map(({name, id, grade}) => ({ id, name,grade }));
console.log(resu);
```

3. 需要弄懂的东西

   1 变量提升和函数提升

   let b=10

   {

   ​	console.log(b);

   ​	//结果输出10

   }

   

   let不会变量提升，var会变量提升

   2 this在普通函数和箭头函数的区分

   3 for in 遍历的时候会把原型公有的也遍历出来

   ​      判断是不是私有的属性用hasOwnProperty

   4 判断变量类型，或者说判断是不是个对象

   ​	let obj = new obj.constructor

   ​	new的时候做了那几件事

   ```
深度拷贝完整版
   
```
   
5。手写一下数组和对象和字符串的常用方法以及去重，差集，交集，还有对象的值和属性
   
占的个数，以及每个方法的属性和对应的参数。
   
6.字符串倒序
   
   str.split().reverse().join()