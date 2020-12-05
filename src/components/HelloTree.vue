<template>
  <div>
    <kr-tree :lazy="true" :load="loadNodes"></kr-tree>
  </div>
</template>

<script>
import KrTree from "@/components/KrTree";

export default {
  name: 'HelloTree',
  components: {KrTree},
  methods: {
    loadNodes(node, loadFunc) {
      let testData = [{text: 'n1'}, {text: 'n2'}, {text: 'n3', isParent: true, children: [{text: 'n3a1'}, {text: 'n3a2'}]}];
      loadFunc(testData);
    }
  }
}
</script>

<style scoped>

</style>
