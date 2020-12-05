<template>
    <div>
        <div class="cell">
            <div :class="cellClass" :arrow-direction="arrowDirection">
                <i @click="doCheck(!checked)" slot="icon" :class="iconClass" :name="checkedIcon"/>
                <div slot="title" @click="doNodeClick">
                    <i v-if="!item.isParent" name="manager" class="van-cell__left-icon blue"/>
                    {{item.text}}
                </div>
            </div>
            <div style="margin-left: 10%" v-if="isParent" v-show="isOpen">
                <kr-tree-node ref="treeNode" :load="load" v-for="(child) in item.children" :key="child.id"
                                :item="child"
                                @node-click="$emit('node-click', $event)"/>
            </div>
        </div>
    </div>
</template>

<script>

    export default {
        name: "KrTreeNode",
        props: {
            // 数据
            item: Object,
            // 如果是懒加载，回调这个函数
            load: Function,
            // 是否懒加载
            lazy: Boolean
        },
        data() {
            return {
                isOpen: false,
                checked: false
            }
        },
        computed: {
            isParent() {
                return this.item.children && this.item.children.length > 0;
            },
            arrowDirection() {
                if (this.item.isParent)
                    return this.isOpen ? 'down' : undefined;
                else
                    return 'none';
            },
            checkedIcon() {
                return this.checked ? 'checked' : 'passed';
            },
            iconClass() {
                return this.checked ? 'van-cell__left-icon blue' : 'van-cell__left-icon';
            },
            cellClass() {
                return this.item.isParent ? '' : '';
            }
        },
        methods: {
            doNodeClick() {
                if (this.item.isParent) {
                    this.isOpen = !this.isOpen;
                    if (this.isOpen) {
                        if (this.lazy) {
                            // 如果懒加载树节点
                            const loadFunc = this.load;
                            loadFunc(this.item, data => {
                                this.$set(this.item, 'children', data);
                            });
                        } else {

                        }
                    }
                } else {
                    this.doCheck();
                }
            },
            doCheck(isCheck) {
                this.checked = isCheck;
                if (this.item.isParent) {
                    let nodeLength = 0;
                    if (this.item.children && (nodeLength = this.item.children.length) > 0) {
                        for (let i = 0; i < nodeLength; i++) {
                            this.$refs['treeNode'][i].doCheck(this.checked);
                        }
                    }
                }
            },
			getCheckedNode() {
				let nodeLength = 0;
				const nodes = [];
				if (this.checked ) {
					nodes.push(this.item);
				}
				if (this.item.children && (nodeLength = this.item.children.length) > 0) {
					for (let i = 0; i < nodeLength; i++) {
						const _nodes = this.$refs['treeNode'][i].getCheckedNode();
						nodes.push.apply(nodes, _nodes);
					}
				}
				return nodes;
			}
        }
    }

</script>

<style scoped>

    .cell {
        background-color: white;
        width: 100%;
    }

    .checked {
        color: #00b7ff;
    }

</style>