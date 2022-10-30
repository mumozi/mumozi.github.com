# vue-element-admin笔记

## 1.导出各种类型

- 拦截器

```javascript
 // blob类型直接生成
      if (Object.prototype.toString.call(res) === '[object Blob]') { // 判断对象是否为`[对象 Blob]`
        if (!res.type.includes('application/json')) { // `application/json` 特殊类型 (可能因为错误造成的)
          const url = window.URL.createObjectURL(new Blob([res], {
            type: response.headers['content-type']
          }))
          // console.log('responseType========', response.headers['content-type'])
          const link = document.createElement('a')
          link.style.display = 'none'
          link.href = url
          link.setAttribute('download', decodeURI(response.headers['content-disposition'].match(/filename=(.*)/)[1]))
          document.body.appendChild(link)
          link.click()
          document.body.removeChild(link)
          return
        } else {
          // blob数据与json数据互相转换
          const reader = new FileReader()
          reader.readAsText(res, 'utf-8')
          reader.onload = e => {
            const readerres = reader.result
            let parseObj = {}
            parseObj = JSON.parse(readerres)
            Message({
              message: parseObj.message,
              type: 'error',
              duration: 5 * 1000
            })
            return
          }
        }
```

- 请求

```js
export function exportPurchase(id) {
  return request({
    url: '/cargoflow/purchase_export/' + id,
    method: 'post',
    responseType: 'blob'
  })
}
```

## 2.新增

```js
    add() {
      this.editForm = Object.assign({}, defaultPermission)
      this.getAllPermission()
      this.title = '新增权限节点'
      this.dialogVisible = true
      this.$nextTick(() => {
        this.$refs.editForm.clearValidate()
      })
    },
```

## 3.修改

```js
    async edit(item) {
      await this.getAllPermission()
      console.log(item)
      this.editForm = Object.assign({}, item, {
        method: item.method.toUpperCase(),
        root: item.p_id === 0,
        is_menu: item.is_menu
      })
      this.checkedPermission = item.p_id
      this.title = '编辑权限 | ' + item.name
      this.dialogVisible = true
      this.$nextTick(() => {
        this.$refs.editForm.clearValidate()
      })
    },
```

## 4.删除

```js
    del(item) {
      this.$confirm('此操作将永久删除, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(() => {
          delUser(item.id).then(() => {
            this.$notify.success('删除成功')
            this.getList()
          })
        })
    }
```

## 5.保存及修改

```js
    submit() {
      this.$refs.editForm.validate((valid) => {
        if (valid) {
          if (this.editForm.id !== undefined) {
            updateUser(this.editForm).then(() => {
              this.$notify.success('修改成功')
              this.getList()
              this.dialogVisible = false
            })
          } else {
            addUser(this.editForm).then(() => {
              this.$notify.success('添加成功')
              this.getList()
              this.dialogVisible = false
            })
          }
        } else {
          return false
        }
      })
    },
```

## 6.查询重置

```js
//搜索
    search() {
      this.filter.page = 1
      this.getList()
    },
//重置
    resetForm() {
      this.filter.page = 1
      this.filter.username = ''
      this.getList()
    },
//方法  
    getList() {
      this.listLoading = true
      getUserList(this.filter).then((response) => {
        const { list, total } = response.data
        this.list = list
        this.total = total
        this.listLoading = false
      })
    },
```

7.新增属性赋值失败

```js
packageChange(packages) {
      packages.forEach(e => {
          //e.num = 1  失效属性
        this.$set(e, 'num', 1)
        this.$set(e, 'price_no_tax', parseFloat(e.price / (1 + e.goods.tax_rate / 100)).toFixed(4))
      })
      this.editForm = Object.assign({}, this.editForm, { packages: packages })
      console.log('%c [ packages ]-535', 'font-size:13px; background:pink; color:#bf2c9f;', packages)
    }
```

8.vue-element-admin侧边栏点击切换svg图标

## **改变图标颜色**

在侧边栏使用自定义图标时，点击菜单会发现图标颜色不会更改。以下有三种方法可以改变颜色。

1、需要找到对应svg，然后删除fill或者改成fill="#......"，一个svg可能有多个fill

2、在iconfont下载图标的时候，使用添加到项目，点击批量操作——批量去色

3、使用css3的滤镜filter中的属性drop-shadow改变svg的颜色