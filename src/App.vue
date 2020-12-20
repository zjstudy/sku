<template>
  <div id="app">
    <div class="content">
      <div class="tab">
        <div class="td" v-for="item in guige">
          <div class="key">{{item.name}}:</div>
          <div class="value"
               v-for="key in item.list"
               :class="{active: key.check, disabled: key.disabled}"
               @click="checkItem(key)">
            {{key.name}}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  components: {},
  data() {
    return {
      res: {},
      guige: [
        {
          name: '颜色',
          list: [
            {name: '红', check: false, disabled: false},
            {name: '白', check: false, disabled: true},
            {name: '蓝', check: false, disabled: false}
          ]
        },
        {
          name: '尺码',
          list: [
            {name: '大', check: false, disabled: false},
            {name: '中', check: false, disabled: false},
            {name: '小', check: false, disabled: false}
          ]
        },
        {
          name: '型号',
          list: [
            {name: 'A', check: false, disabled: false},
            {name: 'B', check: false, disabled: false},
            {name: 'C', check: false, disabled: false}
          ]
        }
      ],
      goods: [
        {
          skuId: '3158054',
          tag: ['红', '大', 'A']
        },
        {
          skuId: '3133859',
          tag: ['白', '中', 'B']
        },
        {
          skuId: '3516833',
          tag: ['蓝', '小', 'C']
        },
      ]
    }
  },
  methods: {
    // 取幂集
    powerset(arr) {
      let ps = [[]];
      for (let i=0; i < arr.length; i++) {
        for (let j = 0, len = ps.length; j < len; j++) {
          ps.push(ps[j].concat(arr[i]));
        }
      }
      return ps;
    },
    // 分解商品数据
    editGoods() {
      let res_key = ''
      let guige = []
      this.goods.map(item => {
        let newarr = this.powerset(item.tag)
        newarr.map(val => {
          if(val.length) {
            res_key = val.join(',')
          } else {
            res_key = ''
          }
          if(!this.res[res_key]) {
            this.res[res_key] = []
          }
          this.res[res_key].push(item.skuId)
        })
        guige = guige.concat(newarr)
      })
      console.log(this.res)
    },
    // 点击规格
    checkItem(data) {
      console.log(data)
      data.check = !data.check
      console.log(data)
    }
  },
  created() {
    this.editGoods()
    // console.log(this.unique(guige))

  }
}
</script>

<style lang="less" scoped>
.content {
  text-align: left;
  .tab{
    .td{
      display: flex;
      line-height: 2;
      margin-bottom: 20px;
      .key{
        margin-right: 20px;
      }
      .value{
        cursor: pointer;
        margin-right: 10px;
        padding: 0 10px;
        border: 1px solid #dbd6d6;
      }
      .active{
        color: #FFF;
        background-color: red;
      }
      .disabled{
        background-color: #b8b8b8;
      }
    }
  }
}
</style>
