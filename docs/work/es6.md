# es6常用语法

## ES5

###  forEach() 没有返回值，只是针对每个元素调用func

```js
arr.forEach(function(elem, index, array) {
    if (arr[i] == 2) {
        continue
    }
    console.log(elem, index)
})
```

> 注意
>
> forEach 的代码块中不能使用 break、continue，它会抛出异常。

### map() 返回新的数组，每个元素为调用func的结果

```js
let result = arr.map(function(value) {
    value += 1
    console.log(value)
    return value
})
console.log(arr, result)
```

###  filter() 返回符合func条件的元素数组

```js
let result = arr.filter(function(value) {
    console.log(value)
    return value == 2
})
console.log(arr, result)
```

###  some() 返回boolean，判断是否有元素符合func条件

```js
let result = arr.some(function(value) {
    console.log(value)
    return value == 4
})
console.log(arr, result)
```

### every() 返回boolean，判断每个元素都符合func条件

```js
let result = arr.every(function(value) {
    console.log(value)
    return value == 2
})
console.log(arr, result)
```

> 注意
>
> every 的代码块中不能使用 break、continue，它会抛出异常

## ES6

### 数组Array

```JS
for (let item of arr) {
    console.log(item)
}

for (let item of arr.values()) {
    console.log(item)
}

for (let item of arr.keys()) {
    console.log(item)
}

for (let [index, item] of arr.entries()) {
    console.log(index, item)
}
```

```JS
Array.of(7); // [7]
Array.of(1, 2, 3); // [1, 2, 3]

Array(7); // [ , , , , , , ]
Array(1, 2, 3); // [1, 2, 3]

Array(5).fill(1)
// [1,1,1,1,1]
```

### 函数Function

```js
function foo(x, y = 'world') {
    console.log(x, y)
}
foo('hello', 0)

```

> TIP
>
> 函数参数是从左到右解析，如果没有默认值会被解析成 undefined

```js
function sum(base, ...nums) {
    let num = base
    nums.forEach(function(item) {
        num += item * 1
    })
    return num
}

console.log(sum(30, 1, 2, 3)) // 36
console.log(sum(30, 1, 2, 3, 4)) // 40
//扩展运算符
function sum(x = 1, y = 2, z = 3) {
    return x + y + z
}

console.log(sum(...[4])) // 9
console.log(sum(...[4, 5])) // 12
console.log(sum(...[4, 5, 6])) // 15

```

### 箭头函数

```js
let hello = (name) => {
    console.log('say hello', name)
}
// 或者

let hello = name => {
    console.log('say hello', name)
}
```

> TIP
>
> - 如果只有一个参数，可以省略括号，如果大于一个参数一定要记得带括号
>
> - 如果返回值是表达式可以省略 return 和 {}
>
> - 如果返回值是字面量对象，一定要用小括号包起来

### 对象Object

####  对象的遍历方式

```js
//标准遍历
for (let key in obj) {
    console.log(key, obj[key])
}
//Object.keys()用于返回对象所有key组成的数组。
Object.keys(obj).forEach(key => {
    console.log(key, obj[key])
})
//Object.getOwnPropertyNames()用于返回对象所有key组成的数组。
Object.getOwnPropertyNames(obj).forEach(key => {
    console.log(key, obj[key])
})
//Reflect.ownKeys()用于返回对象所有key组成的数组。
Reflect.ownKeys(obj).forEach(key => {
    console.log(key, obj[key])
})
```

