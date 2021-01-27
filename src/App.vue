<template>
  <div id="app">
    <div class="content">
      <div class="tab">
        <div class="td" v-for="(item, row) in guige">
          <div class="key">{{item.name}}:</div>
          <div class="value"
               v-for="(key, column) in item.list"
               :class="{active: key.check, disabled: key.disabled, noClick: key.noClick}"
               @click="checkItem(key, row, column)">
            {{key.name}}
          </div>
        </div>
      </div>
      <p>符合选择的产品：{{ checkGoods }}</p>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  components: {},
  data() {
    return {
      res: {}, // 产品幂集子项对应的规格列表
      goodsPs: [], // 产品幂集
      checkGoods: [], // 选中的产品
      checkNowRow: 0, // 点击规格所在行
      checkNowColumn: 0, // 点击规格所在列
      checkItemList: [], // 已选中规格
      showItem: [], // 已选规格对应的幂集子项
      guige: [
        {
          name: '颜色',
          list: [
            {name: '红', value:'1红', check: false, disabled: false, noClick: true},
            {name: '白', value:'1白', check: false, disabled: false, noClick: true},
            {name: '蓝', value:'1蓝', check: false, disabled: false, noClick: true}
          ]
        },
        {
          name: '尺码',
          list: [
            {name: '大', value:'2大', check: false, disabled: false, noClick: true},
            {name: '中', value:'2中', check: false, disabled: false, noClick: true},
            {name: '小', value:'2小', check: false, disabled: false, noClick: true}
          ]
        },
        {
          name: '型号',
          list: [
            {name: 'A', value:'3A', check: false, disabled: false, noClick: true},
            {name: 'B', value:'3B', check: false, disabled: false, noClick: true},
            {name: 'C', value:'3C', check: false, disabled: false, noClick: true}
          ]
        }
      ],
      goods: [
        {
          id: '100001',
          tag: ['1红', '2大', '3A']
        },
        {
          id: '100002',
          tag: ['1红', '2大', '3B']
        },
        {
          id: '100003',
          tag: ['1红', '2中', '3B']
        },
        {
          id: '100004',
          tag: ['1白', '2中', '3B']
        },
        {
          id: '100005',
          tag: ['1白', '2小', '3B']
        },
        {
          id: '100006',
          tag: ['1蓝', '2小', '3C']
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
    // 二维数组去重
    unique(matrix) {
      let res = [];
      matrix.map(item => {
        res.push(item.sort((a, b) => a.localeCompare(b)).toString());
      })
      // return Array.from(new Set(res)).map(item => item.split(','))
      return [...new Set(res)].map(item => item.split(','));// 上下等价
    },
    // 判断数组子集
    subset(A,B){
      A = A.slice();
      for(let i=0, len=B.length; i<len; i++){
        if(A.indexOf(B[i]) === -1){
          return false;
        }else{
          A.splice(A.indexOf(B[i]),1);
        }
      }
      return true;
    },
    // 分解商品数据
    editGoods() {
      let allItem = []
      let res_key = ''
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
          this.res[res_key].push(item.id)
        })
        this.goodsPs = this.goodsPs.concat(newarr)
      })
      this.goodsPs = this.unique(this.goodsPs)
      console.log(this.res, 1)
      console.log(this.goodsPs)
      // 获取所有能显示的规格
      this.goodsPs.map(item => {
        item.map(item2 => {
          allItem.push(item2)
        })
      })
      allItem = Array.from(new Set(allItem))
      this.guige.map(item => {
        item.list.map(val => {
          if(allItem.includes(val.value)){
            val.noClick = false
          } else {
            val.noClick = true
          }
        })
      })
    },
    // 点击规格
    checkItem(data, row, column) {
      this.checkNowRow = row
      this.checkNowColumn = column
      if(!data.disabled && !data.noClick) {
        this.guige[row].list.map(item => {
          item.check = false
        })
        data.check = !data.check
        this.getCheck()
      } else if(data.disabled && !data.noClick) {
        this.guige.map(item => {
          item.list.map(item2 => {
            item2.check = false
          })
        })
        data.check = !data.check
        this.getCheck()
      } if(data.noClick) {
        alert('没有包含该规格的产品')
      }
    },
    // 获取已选择的数据
    getCheck() {
      this.checkItemList = []
      this.guige.map(item => {
        item.list.map(item2 => {
          if(item2.check) {
            this.checkItemList.push(item2.value)
          }
        })
      })
      this.getOnPowerSet()
      this.selectGoods()
    },
    // 设置页面显示状态
    getOnPowerSet() {
      this.showItem = []
      if(this.checkItemList.length > 1) {  // 多选流程
        this.guige.map((item, row) => {
          let showItem = []
          // 判断行是否有选中数据
          let lineCheck = false
          item.list.map((item2, col) => {
            if(item2.check) lineCheck = item2.value
          })

          // 取非当前行的其他选中数据or 全部数据
          let newArr = this.checkItemList.slice(0)
          if(lineCheck) {
            newArr.splice(newArr.indexOf(lineCheck), 1)
          }
          this.goodsPs.map(gItem => {
            if (this.subset(gItem, newArr)) {
              showItem = showItem.concat(gItem)
            }
          })
          showItem = Array.from(new Set(showItem))
          item.list.map(item2 => {
            if(showItem.indexOf(item2.value) !== -1) {
              item2.disabled = false
            } else {
              item2.disabled = true
            }
          })
        })

      } else { // 单选流程
        this.goodsPs.map(item => {
          if(this.subset(item,this.checkItemList)) {
            this.showItem = this.showItem.concat(item)
          }
        })
        this.guige[this.checkNowRow].list.map(item => {
          this.showItem.push(item.value)
        })
        this.showItem = Array.from(new Set(this.showItem))
        this.guige.map(item => {
          item.list.map(val => {
            if(this.showItem.includes(val.value)){
              val.disabled=false
            } else {
              val.disabled=true
            }
          })
        })
      }
    },
    // 获取规格对应的商品
    selectGoods() {
      let checkItem = this.checkItemList.join(',')
      this.checkGoods = this.res[checkItem].join(',')
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
    user-select: none;
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
      .noClick{
        cursor: not-allowed;
        background-color: #999;
        text-decoration: line-through;
      }
    }
  }
}
</style>
