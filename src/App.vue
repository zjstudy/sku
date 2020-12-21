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
      goodsPs: [], // 产品幂集
      checkNowRow: 0, // 点击规格所在行
      checkNowColumn: 0, // 点击规格所在列
      checkItemList: [], // 已选中规格
      showItem: [], // 已选规格对应的幂集子项
      guige: [
        {
          name: '颜色',
          list: [
            {name: '红', check: false, disabled: false, noClick: true},
            {name: '白', check: false, disabled: false, noClick: true},
            {name: '蓝', check: false, disabled: false, noClick: true}
          ]
        },
        {
          name: '尺码',
          list: [
            {name: '大', check: false, disabled: false, noClick: true},
            {name: '中', check: false, disabled: false, noClick: true},
            {name: '小', check: false, disabled: false, noClick: true}
          ]
        },
        {
          name: '型号',
          list: [
            {name: 'A', check: false, disabled: false, noClick: true},
            {name: 'B', check: false, disabled: false, noClick: true},
            {name: 'C', check: false, disabled: false, noClick: true}
          ]
        }
      ],
      goods: [
        {
          skuId: '3158054',
          tag: ['红', '大', 'A']
        },
        {
          skuId: '3158055',
          tag: ['红', '大', 'B']
        },
        {
          skuId: '3158055',
          tag: ['红', '中', 'B']
        },
        {
          skuId: '3133859',
          tag: ['白', '中', 'B']
        },
        {
          skuId: '3133859',
          tag: ['白', '小', 'B']
        },
        {
          skuId: '3516833',
          tag: ['白', '小', 'C']
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
          this.res[res_key].push(item.skuId)
        })
        this.goodsPs = this.goodsPs.concat(newarr)
      })
      this.goodsPs = this.unique(this.goodsPs)
      console.log(this.res)
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
          if(allItem.includes(val.name)){
            val.noClick = false
          } else {
            val.noClick = true
          }
        })
      })
      console.log(allItem)
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
            this.checkItemList.push(item2.name)
          }
        })
      })
      this.getOnPowerSet()
    },
    // 获取选中规格对应的可选择幂集子项
    getOnPowerSet() {
      this.showItem = []
      if(this.checkItemList.length > 1) {  // 多选流程
/*        this.checkItemList.map((item,index) => {
          let newArr = this.checkItemList.slice(0)
          newArr.splice(index,1)
          this.goodsPs.map(item => {
            if(this.subset(item,newArr)) {
              this.showItem = this.showItem.concat(item)
            }
          })
        })
        this.checkItemList.map((item,index) => {
          let newArr = this.checkItemList.slice(0)
          newArr.splice(index,1)
          this.goodsPs.map(item => {
            if(this.subset(item,newArr)) {
              this.showItem = this.showItem.concat(item)
              console.log('单项')
              console.log(item)
            }
          })
        })*/
        console.log(this.goodsPs)
        this.guige.map((item, row) => {
          console.log(`${row}行str------------------------`)
          let showItem = []
          // 判断行是否有选中数据
          let lineCheck = false
          item.list.map((item2, col) => {
            if(item2.check) lineCheck = item2.name
          })

          // 取非当前行的其他选中数据or 全部数据
          let newArr = this.checkItemList.slice(0)
          if(lineCheck) {
            newArr.splice(newArr.indexOf(lineCheck), 1)
          }
          // console.log(newArr)
          this.goodsPs.map(gItem => {
            if (this.subset(gItem, newArr)) {
              showItem = showItem.concat(gItem)
            }
          })
          console.log('集合')
          showItem = Array.from(new Set(showItem))
          console.log(showItem)
          item.list.map(item2 => {
            if(showItem.indexOf(item2.name) !== -1) {
              console.log('显示')
              console.log(item2.name)
              item2.disabled = false
            } else {
              item2.disabled = true
            }
          })
          // showItem = Array.from(new Set(showItem))
          // console.log(showItem)



        /*  this.checkItemList.map((checkItem,index) => {
            let newArr = this.checkItemList.slice(0)
            if(lineCheck) {
              newArr.splice(newArr.indexOf(lineCheck), 1)
            }
            console.log('')
            console.log(newArr)
            this.goodsPs.map(gPsItem => {
              if (this.subset(gPsItem, newArr)) {
                showItem = this.showItem.concat(gPsItem)
              }
            })
            showItem = Array.from(new Set(showItem))
            console.log(showItem)



/!*
            this.goodsPs.map(item => {
              if(this.subset(item,newArr)) {
                this.showItem = this.showItem.concat(item)
                console.log('单项')
                console.log(item)
              }
            })*!/
          })*/
          console.log(`${row}行end------------------------`)
        })

      } else { // 单选流程
        this.goodsPs.map(item => {
          if(this.subset(item,this.checkItemList)) {
            this.showItem = this.showItem.concat(item)
          }
        })
        this.guige[this.checkNowRow].list.map(item => {
          this.showItem.push(item.name)
        })
        this.showItem = Array.from(new Set(this.showItem))
        this.showItemFn()
      }
    },
    // 设置页面按钮状态
    showItemFn() {
      console.log('显示的规格')
      console.log(this.showItem)
      console.log('-----------------')
      this.guige.map(item => {
        item.list.map(val => {
          if(this.showItem.includes(val.name)){
            val.disabled=false
          } else {
            val.disabled=true
          }
        })
      })
    }
  },
  created() {
    this.editGoods()
    let a = [1,'红色1',3,4]
    let b = [4,1,'红色']
    console.log(this.subset(a,b))
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
