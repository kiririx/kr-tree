<template>
  <div>
    <div v-if="items.length > 0" class="check-all">
      <i @click="doCheckAll" :class="iconClass" :name="checkedIcon"/>
      <label>全选</label>
    </div>
<!--    <van-loading v-if="loading && items.length < 1" style="left: 35%;top: 50px;" size="24px">加载中...</van-loading>-->
    <p v-if="!loading && items.length < 1" class="none-data">无数据</p>
    <kr-tree-node :key="item.key" ref="treeNode" :lazy="lazy" :load="load" v-for="item in items" :item="item"/>
  </div>
</template>

<script>
import KrTreeNode from "@/components/KrTreeNode";

export default {
  name: "examTree",
  components: {KrTreeNode},
  props: {
    load: Function,
    lazy: Boolean,
    treeData: Array,
    loading: Boolean
  },
  data() {
    return {
      items: [],
      checked: false
    }
  },
  computed: {
    checkedIcon() {
      return this.checked ? 'checked' : 'passed';
    },
    iconClass() {
      return this.checked ? 'van-cell__left-icon blue' : 'van-cell__left-icon';
    },
  },
  methods: {
    onLoad() {
      const loadFunc = this.load;
      loadFunc(null, (data) => {
        this.items = data;
      });
    },
    doCheckAll() {
      this.checked = !this.checked;
      let nodeLength = 0;
      if (this.items && (nodeLength = this.items.length) > 0) {
        for (let i = 0; i < nodeLength; i++) {
          const treeNode = this.$refs['treeNode'][i];
          if (treeNode) {
            treeNode.doCheck(this.checked);
          }
        }
      }
    },
    getCheckedNodes() {
      let nodeLength = 0;
      const nodes = [];
      if (this.items && (nodeLength = this.items.length) > 0) {
        for (let i = 0; i < nodeLength; i++) {
          const treeNode = this.$refs['treeNode'][i];
          if (treeNode) {
            const _nodes = treeNode.getCheckedNode();
            nodes.push.apply(nodes, _nodes);
          }
        }
      }
      return nodes;
    },
    isShowNode(item) {
      return !item.isParent || (item.children && item.children.length > 0)
    }
  },
  mounted() {
    if (this.lazy)
      this.onLoad();
  },
  watch: {
    treeData: {
      handler() {
        this.items = this.treeData;
      },
      immediate: true
    }
  }
}
</script>

<style scoped>
.check-all {
  margin: 10px 5px;
  font-size: 14px;
  display: flex;
  align-items: center;
}

.checked {
  color: #00b7ff;
}

.none-data {
  margin: 50px 0 0 40%;
}
</style>