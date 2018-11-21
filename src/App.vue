<template>
    <div id="app">
        <h1>{{title}}</h1>
        <div class="viewSearchClass">
            搜索栏 <button class="button button-primary" @click="handleAdd()">添加</button>
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
                            <button class="button button-success" @click="handleEdit(item,index)">修改</button>
                            <button class="button button-danger" @click="handleDelet(item,index)">删除</button>
                        </td>
                    </tr>
                </tbody>
            </table>
            <p v-else>暂无数据</p>
        </div>

        <div v-show="viewAlert.show" class="showAlert">{{viewAlert.info}}</div>
        <div v-show="viewAdd.show" class="viewAdd" id="viewAdd">
            <div class="modal-mask"></div>
            <div class="modal-wrap">
                <div class="modal" style="width: 520px;">
                    <div class="modal-content">
                        <i class="modal-close" @click="viewAdd.show = false">X</i>
                        <div class="modal-header">
                            <div class="modal-header-inner">新加</div>
                        </div>
                        <div class="modal-body">
                            <p>
                                <label for="data">日期</label>
                                <input v-model="viewAdd.item['data']" type="date"/>
                            </p>
                            <p>
                                <label for="name">姓名</label>
                                <input v-model="viewAdd.item['name']" placeholder="请输入姓名"/>
                            </p>
                            <p>
                                <label for="province">上海</label>
                                <select v-model="viewAdd.item['province']">
                                    <option value="0">无</option>
                                    <option value="上海">上海</option>
                                    <option value="北京">北京</option>
                                    <option value="河北">河北</option>
                                </select>
                            </p>
                            <p>
                                <label for="city">普陀区</label>
                                <select v-model="viewAdd.item['city']">
                                    <option value="0">无</option>
                                    <option value="普陀区">普陀区</option>
                                    <option value="朝阳区">朝阳区</option>
                                    <option value="石家庄">石家庄</option>
                                </select>
                            </p>
                            <p>
                                <label for="address">地址</label>
                                <textarea v-model="viewAdd.item['address']" placeholder="请输入地址"/>
                                </textarea>
                            </p>
                            <p>
                                <label for="zip">邮编</label>
                                <input v-model="viewAdd.item['zip']" placeholder=""/>
                            </p>
                        </div>
                        <div class="modal-footer">
                            <button class="button button-default" @click="viewAdd.show = false">取消</button>
                            <button class="button button-info" @click="addSingle()">确定</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div v-show="viewEdit.show" class="viewEdit" id="viewEdit">
            <div class="modal-mask"></div>
            <div class="modal-wrap">
                <div class="modal" style="width: 520px;">
                    <div class="modal-content">
                        <i class="modal-close" @click="viewEdit.show = false">X</i>
                        <div class="modal-header">
                            <div class="modal-header-inner">修改</div>
                        </div>
                        <div class="modal-body">
                            <p v-for="(item,index) in viewEdit.copy" :key="index">
                                <label :for="index">{{index | keyToCN}}</label>
                                <input v-model=" viewEdit.copy[index]" placeholder="" />
                            </p>
                        </div>
                        <div class="modal-footer">
                            <button class="button button-default" @click="viewEdit.show = false">取消</button>
                            <button class="button button-info" @click="editSingle()">确定</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div v-show="viewDelet.show" class="viewDelet" id="viewDelet">
            <div class="modal-mask"></div>
            <div class="modal-wrap">
                <div class="modal" style="width: 520px;">
                    <div class="modal-content">
                        <i class="modal-close" @click="viewDelet.show = false">X</i>
                        <div class="modal-header">
                            <div class="modal-header-inner">删除</div>
                        </div>
                        <div class="modal-body">
                            确定删除吗?
                        </div>
                        <div class="modal-footer">
                            <button class="button button-default" @click="viewDelet.show = false">取消</button>
                            <button class="button button-info" @click="deletSingle()">确定</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from "axios";
export default {
  name: "App",
  components: {},
  data() {
    return {
      //模版版本号
      modelVersion: "0.0.0",
      //页面版本
      pageVersion: "0.0.0",
      //页面标题
      title: "页面标题",
      //主题 *need
      theme: {
        //主题文件路径
        path: "./assets/themes",
        //主题文件夹名称
        name: "theme1"
      },
      //页面表格
      viewTable: {
        //dom元素id
        domId: "",
        //样式表类名
        className: "",
        page: {
          //当前页码
          p: 1,
          //数据总数量
          count: 0,
          //每页包含数据量
          size: 15
        },
        //数据
        data: []
      },
      //接口信息 *need
      ajax: {
        // 查询
        Search: {
          //接口地址
          action: "",
          //提交方式
          method: "",
          // 参数
          params: {}
        },
        // 增加
        add: {
          //接口地址
          action: "",
          //提交方式
          method: "",
          // 参数
          params: {}
        },
        // 修改
        edit: {
          //接口地址
          action: "",
          //提交方式
          method: "",
          // 参数
          params: {}
        },
        // 删除
        delete: {
          //接口地址
          action: "",
          //提交方式
          method: "",
          // 参数
          params: {}
        }
      },
      //页面搜索栏
      viewSearch: {},
      //数据新增功能
      viewAdd: {
        item: {
            data:"",
            name:"",
            province:"",
            city:"",
            address:"",
            zip:""
        },
        show:false
      },
      //数据修改功能
      viewEdit: {
        index: "",
        copy: "",
        show: false
      },
      //数据删除功能
      viewDelet: {
        show: false,
        item: {},
        index: ""
      },
      //弹窗消息内容
      viewAlert: {
        // 弹窗内容
        info: "",
        // 是否显示
        show: false
      }
    };
  },
  created() {
    //获取主题样式表
    this.getTheme(this.theme.path, this.theme.name);
    //   获取表格数据
    this.viewTable.data = this._getViewTableData(
      this.ajax.Search.action,
      this.ajax.Search.method,
      this.ajax.Search.params
    );
  },
  methods: {
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
    getTheme(path, themeName) {
      if (!path || !themeName) {
        console.log("没有加载主题");
        return false;
      }
      console.log(path + "/" + themeName + "/index.less");

      require(path + "/" + themeName + "/index.less");
    },

    _getViewTableData() {
      return [
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
      ];
    },
    // 弹窗显示
    /**
     * @function () showAlert(info,wait)
     * @description 弹窗提示  待修改
     * @param {Object} info 提示内容
     * @param {Object} wait 等待消逝的时间
     */
    showAlert(info, wait) {
      this.viewAlert.info = info;
      this.viewAlert.show = true;
      if (!wait) {
        wait = 1000;
      }
      setTimeout(() => {
        this.viewAlert.show = false;
      }, wait);
    },
    //表格操作
    /**
     * @function () handleEdit(item)
     * @description 表格 修改弹窗
     * @param {Object} item 表格一行数据
     * @param {String} index 数据第几项
     */
    handleEdit(item, index) {
      console.log("edit", item);
      this.viewEdit.copy = Object.assign({}, item);
      this.viewEdit.index = index;
      this.viewEdit.show = true;
    },
    /**
     * @function () editSingle()
     * @description 修改单项
     */
    editSingle() {
      this.viewTable.data[this.viewEdit.index] = this.viewEdit.copy;
      this.viewEdit.show = false;
    },
    /**
     * @function () handleDelet(item)
     * @description 表格 修改数据
     * @param {Object} item 表格一行数据
     */
    handleDelet(item, index) {
      console.log("delet", item);
      this.viewDelet.item = item;
      this.viewDelet.index = index;
      this.viewDelet.show = true;
    },
    /**
     * @function () deleteSingle()
     * @description 删除单项
     */
    deletSingle() {
      this.viewTable.data.splice(this.viewDelet.index, 1);
      this.viewDelet.show = false;
    },

    /**
     * @function () handleDelet(item)
     * @description 表格 修改数据
     * @param {Object} item 表格一行数据
     */
    handleAdd(item) {
      console.log("添加新成员");
      this.viewAdd.show = true;
    },
    /**
     * @function () addeSingle()
     * @description 添加单项
     */
    addSingle() {
        var o = Object.assign({}, this.viewAdd.item);        
        this.viewTable.data.push(o);
        for ( var i in this.viewAdd.item) {
             this.viewAdd.item[i] = "";
        }
        this.viewAdd.show = false;
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
    getViewTableData(path, type, params) {
      if (type == "get") {
        axios
          .get(path, params)
          .then(function(data) {
            return data.data;
          })
          .catch(function(error) {
            this.showAlert(error);
          });
      } else if (type == "post") {
        axios
          .post(path, params)
          .then(function(data) {
            return data.data;
          })
          .catch(function(error) {
            this.showAlert(error);
          });
      }
    }
  },
  filters: {
    //   转换key为中文显示名
    keyToCN(key) {
      //中英参照   *need
      var tableKeys = [
        {
          key: "name",
          cn: "姓名"
        }
      ];
      for (var index = 0; index < tableKeys.length; index++) {
        var element = tableKeys[index];
        if (element.key == key) {
          return element.cn;
        }
      }
      return key;
    }
  }
};
</script>

<style lang="less">
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
