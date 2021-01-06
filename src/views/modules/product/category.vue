<template>
  <el-tree
    :data="menus"
    :props="defaultProps"
    node-key="catId"
    :expand-on-click-node="false"
    show-checkbox
    :default-expanded-keys="expandedKeys"
  >
    <span class="custom-tree-node" slot-scope="{ node, data }">
      <span>{{ node.label }}</span>
      <span>
        <el-button
          v-if="node.level <= 2"
          type="text"
          size="mini"
          @click="() => append(data)"
        >
          Append
        </el-button>
        <el-button
          v-if="node.childNodes.length == 0"
          type="text"
          size="mini"
          @click="() => remove(node, data)"
        >
          Delete
        </el-button>
      </span>
    </span>
  </el-tree>
</template>

<script>
export default {
  components: {},
  data() {
    return {
      //default-expanded-keys	默认展开的节点的 key的数组（array）  :default-expanded-keys等同于v-bind:default-expanded-keys
      expandedKeys: [],
      menus: [],
      defaultProps: {
        children: "children",
        label: "name",
      },
    };
  },
  computed: {},
  watch: {},
  methods: {
    // 获取数据列表
    getMenus() {
      this.$http({
        url: this.$http.adornUrl("/product/category/tree"),
        method: "get",
      }).then(({ data }) => {
        this.menus = data.data;
      });
    },
    append(data) {
      console.log(data);
    },

    remove(node, data) {
      console.log(node, data);
      var ids = [data.catId];
      //弹框
      this.$confirm(`确定删除【${data.name}】菜单`, "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          this.$http({
            url: this.$http.adornUrl("/product/category/delete"),
            method: "post",
            data: this.$http.adornData(ids, false),
          })
            .then(() => {
              this.getMenus();
              //保持展开状态
              this.expandedKeys = [node.parent.data.catId];
            })
            .catch();
        })
        .then(() => {
          //消息提示
          this.$message({
            type: "success",
            message: "删除成功!",
          });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除",
          });
        });
    },
  },
  created() {
    this.getMenus();
  },
  mounted() {},
  beforeCreate() {},
  beforeMount() {},
  beforeUpdate() {},
  updated() {},
  beforeDestroy() {},
  destroyed() {},
  activated() {},
};
</script>
<style scoped>
</style>