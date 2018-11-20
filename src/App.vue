<template>
    <div id="app">
        <h1>{{title}}</h1>
        <div class="viewSearchClass">
            搜索栏 <button @click="handleAdd()">添加</button>
        </div>
        <div class="viewTableClass">
            <table v-if="viewTable.data.length" :id="viewTable.domId" :class="viewTable.className">
                <thead>
                    <tr>
                        <th v-for="(i,j) in viewTable.data[0]" :key="i">{{j | keyToCN }}</th>
                        <th>操作</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(item,index) in viewTable.data" :key="index">
                        <td v-for="(i,j) in item" :key="j">{{i}}</td>
                        <td>
                            <button @click="handleEdit(item)">修改</button>
                            <button @click="handleDelet(item)">删除</button>
                            </td>
                    </tr>
                </tbody>
            </table>
            <p v-else >暂无数据</p>
        </div>
       
     <p>{{viewTable.data}}</p>
     <div v-show="viewAlert.show" class="showAlert">{{viewAlert.info}}</div>
     <div v-show="viewAdd.show" class="viewAdd" id="viewAdd">
            <!-- <p v-for="">
                <label for=""></label>
                <input v-model="" placeholder=""></input>
            </p> -->
     </div>
    </div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'App',
  components: {
  },
  data() {
    return{
        //模版版本号
        modelVersion: "0.0.0", 
        //页面版本
        pageVersion:"0.0.0",
        //页面标题
        title:"页面标题",
        //主题
        theme:{
            //主题文件路径
            path:'./assets/themes',
            //主题文件夹名称
            name:"theme1"
            
        },
        //页面表格
        viewTable:{
            //dom元素id
            domId:"",
            //样式表类名
            className:"",
            //数据
            data:[]
        },
        //表格第一行属性列表 中英参照
        tableKeys:[
            {
                key:"name",
                cn:"姓名"
            }
        ],
        //页面搜索栏
        viewSearch:{
            ajax:{
                //接口地址
                action: "",
                //提交方式
                method: "",
                params:{}
            }
        },
        //页面新增功能
        viewAdd:{

        },
        //页面修改功能
        viewEdit:{

        },
        //页面删除功能
        viewDelet:{

        },
        //页面弹窗消息内容
        viewAlert:{
            // 弹窗内容
            info:"",
            // 是否显示
            show:"false"
        }
    }
  },
  created() {
        //获取主题样式表
        this.getTheme(this.theme.path,this.theme.name);
        //   获取表格数据
        this.viewTable.data = this._getViewTableData(this.viewSearch.ajax.action,this.viewSearch.ajax.method,this.viewSearch.ajax.params);
  },
  methods:{
      //校验表单
    // checkForm(e) {
    //     if (this.edit.formData.product.value && this.edit.formData.date.value) {
    //         return true;
    //   }

    //   this.edit.errors = [];

    //   if (!this.edit.formData.product.value) {
    //       this.edit.errors.push('请填写产品名称！');
    //   }
    //   if (!this.edit.formData.date.value) {
    //       this.edit.errors.push('请填写出厂日期!');
    //   }

    //   e.preventDefault();
    // },
    /**
     * @function {} getTheme(path , themeName)
     * @description 获取主题样式表，文件类型为less
     * @param {String} path 主题文件夹路径
     * @param {String} themeName 主题名称 
     */
    getTheme(path , themeName) {
        if (!path||!themeName) {
            console.log("没有加载主题");
            return false;
        }
        console.log(path+'/'+themeName+'/index.less');
        
        require(path+'/'+themeName+'/index.less');
    },
    
    _getViewTableData(){
        return  [
        {
          date: "2016-05-03",
          name: "王小虎",
          province: "上海",
          city: "普陀区",
          address: "上海市普陀区金沙江路 1518 弄",
          zip: 200333
        },
        {
          date: "2016-05-02",
          name: "王小虎",
          province: "上海",
          city: "普陀区",
          address: "上海市普陀区金沙江路 1518 弄",
          zip: 200333
        },
        {
          date: "2016-05-04",
          name: "王小虎",
          province: "上海",
          city: "普陀区",
          address: "上海市普陀区金沙江路 1518 弄",
          zip: 200333
        },
        {
          date: "2016-05-01",
          name: "王小虎",
          province: "上海",
          city: "普陀区",
          address: "上海市普陀区金沙江路 1518 弄",
          zip: 200333
        }
      ]
    },
// 弹窗显示
     /**
     * @function () showAlert(info,wait)
     * @description 弹窗提示  待修改
     * @param {Object} info 提示内容
     * @param {Object} wait 等待消逝的时间
     */
    showAlert(info,wait){
        this.viewAlert.info = info;
        this.viewAlert.show = true;
        if (!wait) {
            wait =  1000;
        }
        setTimeout(()=>{this.viewAlert.show = false}, wait);
    },
//表格操作
    /**
     * @function () handleEdit(item)
     * @description 表格 修改数据
     * @param {Object} item 表格一行数据
     */
    handleEdit(item){
        console.log("edit",item);
        this.showAlert(item,2000)
    },
    /**
     * @function () handleDelet(item)
     * @description 表格 修改数据
     * @param {Object} item 表格一行数据
     */
    handleDelet(item){
        console.log("delet",item);
        this.showAlert(item)
    },
    /**
     * @function () handleDelet(item)
     * @description 表格 修改数据
     * @param {Object} item 表格一行数据
     */
    handleAdd(item){
        console.log("添加新成员");
    },
//接口
    /**
     * @function () getViewTableData(path,type,params)
     * @description 查询表格数据
     * @param {String} path 地址
     * @param {String} type 提交方式
     * @param {Object} params 传参
     * @returns {function} axios.post()||axios.get()
     */
    getViewTableData(path,type,params){
        if (type=="get") {
            axios.get(path,params)
                .then(function(data){return data.data;})
                .catch(function(error){this.showAlert(error);});
        }else if(type=="post"){
            axios.post(path,params)
                .then(function(data){return data.data;})
                .catch(function(error){this.showAlert(error);});
        }
    },
    /**
     * @function () addViewTableData(path,type,params)
     * @description 添加表格数据
     * @param {String} path 地址
     * @param {String} type 提交方式
     * @param {Object} params 传参
     * @returns {function} axios.post()||axios.get()
     */
    addViewTableData(path,type,params){
        if (type=="get") {
            axios.get(path,params)
                .then(function(data){return data.data;})
                .catch(function(error){this.showAlert(error);});
        }else if(type=="post"){
            axios.post(path,params)
                .then(function(data){return data.data;})
                .catch(function(error){this.showAlert(error);});
        }
    },
    /**
     * @function () getViewTableData(path,type,params)
     * @description 查询表格数据
     * @param {String} path 地址
     * @param {String} type 提交方式
     * @param {Object} params 传参
     * @returns {function} axios.post()||axios.get()
     */
    getViewTableData(path,type,params){
        if (type=="get") {
            axios.get(path,params)
                .then(function(data){return data.data;})
                .catch(function(error){this.showAlert(error);});
        }else if(type=="post"){
            axios.post(path,params)
                .then(function(data){return data.data;})
                .catch(function(error){this.showAlert(error);});
        }
    }
    
  },
  filters: {
    //   转换key为中文显示名
      keyToCN(key){
          var tableKeys = [
              {
                key:"name",
                cn:"姓名"
            }
          ];
          for (var index = 0; index < tableKeys.length; index++) {
              var element = tableKeys[index];
              console.log(element)
              if (element.key == key) {
                  return element.cn;
              }
          }
          return key;
      }
  }
}
</script>

<style lang="less">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
