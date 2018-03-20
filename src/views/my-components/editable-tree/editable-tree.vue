<style lang="less">
    @import './editable-tree.less';
</style>

<template>
    <div class="editable-tree">
        <Tree :data="tree.data" :render="renderContent"></Tree>
    </div>
</template>

<script>
    import util from '@/libs/util';
    import Node from './node.vue';

    export default {
        props: {
            domainUrl: String,
            selectedId: String,
            rootName: {
                type: String,
                default: '全部'
            },
            dataType: {
                type: String,
                validator(val) {
                    return 'list' === val || 'tree' === val;
                },
                default: 'list'
            }
        },
        data() {
            return {
                tree: {
                    raw: [],
                    data: [],
                    selected: {id: this.selectedId}
                }
            }
        },
        methods: {
            loadTreeData() {
                let ajax = util.ajax.get(`/api/${this.domainUrl}`);
                ajax.then((response)=>{
                    // 如果树刷新需要重新选中之前选中的节点
                    response.data.forEach((n)=>{
                        n.selected = n.id === this.tree.selected.id;
                        n.title = n.name;
                    });
                    this.tree.raw = response.data;
                    this.tree.data = [
                        {
                            id: null,
                            selected: this.tree.selected.id === null,
                            title: this.rootName,
                            expand: true,
                            _root: true,
                            children: [...response.data]
                        }
                    ];
                });
                return ajax;
            },
            renderContent(h, { root, node, data }) {
                return h(Node, {
                    props: {
                        value: data,
                        domainUrl: this.domainUrl,
                        dataType: this.dataType,
                        treeData: this.tree.raw
                    },
                    on: {
                        'on-node-click': () => {
                            this.selectNode(data.id);
                        },
                        'on-save-success': () => {
                            this.loadTreeData();
                        },
                        'on-delete-success': () => {
                            this.loadTreeData();
                        }
                    }
                });
            },
            selectNode(id) {
                let data = this.tree.raw.find((d)=>d.id === id);
                this.tree.selected.selected = false;
                this.tree.selected = data;
                data.selected = true;
                this.$emit('on-select-change', [data]);
            }
        },
        mounted() {
        }
    }
</script>