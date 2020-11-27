# vue-element-
vue+element实现动态表格
```html
<template>
  <div class='center_box'>
    <div class='con_title'>动态表格</div>
    <div class='con_table'>
      <el-row>
        <el-row>
          <el-col>
            <el-checkbox-group v-model="checkArr" size="medium">
              <el-col :span=12 v-for="item in optionalColumnList" :key="item.prop">
                <el-row :gutter=20>
                  <el-col>
                    <el-col :span=2><el-checkbox :label="item.propName" @change="menuChange(item)" ></el-checkbox></el-col>
                  </el-col>
                </el-row>
              </el-col>
            </el-checkbox-group>
          </el-col>
        </el-row>
      </el-row>
      <el-table ref="multipleTable" :data="showList" v-loading="listLoading" height="500">
        <el-table-column :label="item.propName" :property="item.prop" v-for="item in tableColumnList" :key="item.prop" align="center">
          <template slot-scope="scope">
            <span>{{scope.row[scope.column.property]}}</span>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <p v-html="this.price"></p>
    <p>{{times.substring(0,10)}}</p>
  </div>
</template>
```
```js
<script>
export default {
  name: '',
  data () {
    return {
      listLoading: false,
      showList: [],
      // 这里为了简便我就没有调用后台接口获取数据，直接写的假数据  你要用的话可以调用后台接口获取tableColumnList，注意数据格式
      tableColumnList: [{prop: 'id', propName: '编号'},
        {prop: 'name', propName: '名字'},
        {prop: 'age', propName: '保质期'},
        {prop: 'remark', propName: '特点'}],
      // 这里为了简便我就没有调用后台接口获取数据，直接写的假数据
      dataList: [{'id': '100001','name': '小红萝卜','age': '2年','remark': '适合油炸','country': '中国','address': '广东省深圳市'},
        {'id': '100002','name': '萝卜妹','age': '2年','remark': '适合水煮','country': '美国','address': '硅谷'},
        {'id': '100003','name': '胖萝卜头','age': '1年','remark': '适合玩儿','country': '泰国','address': '清迈'},
        {'id': '100004','name': '萝卜酱','age': '4年','remark': '适合吃火锅','country': '韩国','address': '首尔'}],
      optionalColumnList: [{prop: 'country', propName: '出口国家'},
        {prop: 'address', propName: '零售点'}],
      checkArr:[],
      price:'12.00',
      times:'2017-12-31 23:23:00',
    }
  },
  methods: {
    initTable () {
      // 配置表头
      let selHeader = this.tableColumnList;
      let tableHeader = []; // 组合动态的表头
      selHeader.forEach(item => {
        let tabHeader = {};
        tabHeader.prop = item.prop;
        tabHeader.propName = item.propName;
        tableHeader.push(tabHeader);
      });
      this.tableColumnList = tableHeader;
      this.showList = this.dataList
    },
    menuChange(item){
      // 注意  我这里都用的假数据，要从后台获取tableColumnList和dataList的时候
      // 每一次调用menuChange都要重新获取tableColumnList和dataList，保证属性和数据是对应的
      let flag = true
      for(var i=0;i<this.checkArr.length;i++){
        if(this.checkArr[i] === item.propName){
          flag = false
          break
        }
      }
      if(!flag){
        this.tableColumnList.push(item)
      }
      if(flag){
        Array.prototype.contains = function(obj) {
          var j = this.length;
          while (j--) {
            if (this[j] === obj) {
              return j; // 返回的这个 i 就是元素的索引下标，
            }
          }
          return false
        }
        this.tableColumnList.splice(this.tableColumnList.contains(item),1)
      }
    },
  },
  beforeUpdate (){
    this.$nextTick(() => { //在数据加载完，重新渲染表格 ,避免动态配置表格抖动问题
      this.$refs['multipleTable'].doLayout();
    })
  },
  mounted () {
    this.initTable()
    //1.分割价钱 => ["12", "00"]
    let splitPrice = this.price.split(".");
    //2.重新赋值
    this.price = `${splitPrice[0]}.<span style="font-size:12px;">${splitPrice[1]}</span>`;
  }
}
</script>
```

