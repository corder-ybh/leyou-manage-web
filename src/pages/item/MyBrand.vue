<template>
  <v-card>
    <v-card-title flat color="white">
      <v-btn color="primary" @click="addBrand">新增品牌</v-btn>
      <v-spacer />
      <!--搜索框，与search属性关联-->
      <v-text-field label="输入关键字搜索" v-model="search" append-icon="search" hide-details/>
    </v-card-title>
    <v-data-table
    :headers="headers"
    :items="brands"
    :pagination.sync="pagination"
    :total-items="totalBrands"
    :loading="loading"
    class="elevation-1">
      <template slot="items" slot-scope="props">
        <td class="text-xs-center">{{ props.item.id }}</td>
        <td class="text-xs-center">{{ props.item.name }}</td>
        <td class="text-xs-center">
          <img v-if="props.item.image" :src="props.item.image" width="130" height="40" />
        </td>
        <td class="text-xs-center">{{ props.item.letter }}</td>
        <td class="text-xs-center">
          <v-btn color="info" @click="editBrand(props.item)">编辑</v-btn>
          <v-icon small @click="deleteItem(props.item)">
            delete
          </v-icon>
        </td>
      </template>
    </v-data-table>
    <!--弹出的对话框-->
    <v-dialog max-width="500" v-model="show" persistent>
      <v-card>
        <!--对话框标题-->
        <v-toolbar dense dark color="primary">
          <v-toolbar-title>{{isEdit ? "修改" : "新增"}}品牌</v-toolbar-title>
          <v-spacer/>
          <!--关闭窗口的按钮-->
          <v-btn icon @click="closeWindow"><v-icon>close</v-icon></v-btn>
        </v-toolbar>
        <!--对话框的内容，表单-->
        <v-card-text class="px-5">
          <my-brand-form @close="closeWindow" :oldBrand="oldBrand" :isEdit="isEdit"></my-brand-form>
        </v-card-text>
      </v-card>
    </v-dialog>
  </v-card>
</template>

<script>
  // 导入自定义表单组件
  import MyBrandForm from './MyBrandForm';
  export default {
    name : "myBrand",
    components: {
      MyBrandForm,
    },
    data() {
      return {
        totalBrands: 0, // 总条数
        brands: [], // 当前页品牌数据
        search: "", // 查询关键字
        loading: true, // 是否在加载中
        pagination: {}, // 分页信息
        headers: [ // 头信息
          {text: 'id', align: 'center', value:'id'},
          {text: '名称', align: 'center', value:'name', sortable: false},
          {text: 'LOGO', align: 'center', value:'image', sortable: false},
          {text: '首字母', align: 'center', value:'letter'},
          {text: '操作', align: 'center', value: 'id', sortable: false}
        ],
        show: false, // 控制对话框的显示
        oldBrand: {}, // 即将被编辑的品牌数据
        isEdit: false
      }
    },
    methods: {
      // 编辑品牌
      editBrand(oldBrand){
        // 根据品牌信息查询商品分类
        this.$http.get("/item/category/bid/" + oldBrand.id)
          .then(({data}) => {
            // 修改标记
            this.isEdit = true;
            // 控制窗口可见
            this.show = true;
            // 获取要编辑的brand
            this.oldBrand = oldBrand;
            this.oldBrand.categories = data;
          })
      },
      getDataFromServer() { // 从服务端加载数据
        this.loading = true; // 加载数据
        // 通过axios获取数据
        this.$http.get("/item/brand/page", {
          params: {
            page: this.pagination.page, // 当前页
            rows: this.pagination.rowsPerPage, // 每页条数
            sortBy: this.pagination.sortBy, // 排序字段
            desc: this.pagination.descending, // 是否降序
            key: this.search // 查询字段
          }
        }).then(resp => { // 获取响应结果对象
          this.totalBrands = resp.data.total; // 总条数
          this.brands = resp.data.items; // 品牌数据
          this.loading = false; // 加载完成
        })
      },
      addBrand() {
        // 添加是否是编辑标签
        this.isEdit = false;
        // 控制弹窗可见
        this.show = true;
        // 清空之前的数据
        this.oldBrand = null;
      },
      closeWindow() {
        // 关闭窗口
        this.show = false;
        // 重新加载数据
        this.getDataFromServer();
      }
    },
    // 渲染后执行
    mounted() {
      this.getDataFromServer();// 调用数据初始化函数
    },
    watch: {
      pagination: { // 监视pagination属性的变化
        deep: true, // deep为true，会监视pagination的属性及属性中的对象属性变化
        handler() {
          // 变化后的回调函数，这里我们再次调用getDataFromServer
          this.getDataFromServer();
        }
      },
      search: {
        handler() {
          this.getDataFromServer();
        }
      }
    }
  }
</script>

<!-- scoped: 当前样式只作用于当前组件 -->
<style scoped>
</style>
