# sku
基于VUE的商品规格SKU


## 启动项目
```
npm install
```

```
npm run serve
```

# 注意事项
实际应用中如果页面数据有数组对象嵌套，可能会发生数据更改后视图未刷新的情况，
建议在data中赋值之前，先深拷贝一次数据再赋值，如下，当然也有其它方式可自行处理
```
methods: {
  // 请求数据
  getData() {
    // XXXXX
    this.list = JSON.parse(JSON.stringify(XXX))
  }
}
```
