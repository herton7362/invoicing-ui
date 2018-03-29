<style lang="less">
    @import './editable-tree.less';
</style>

<template>
    <div class="editable-tree">
        <Tree :data="tree.data" :render="renderContent" @toggle-expand="toggleExpand"></Tree>
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
            },
            treeTransformResponse: {
                type: Function,
                default(response) {
                    return response
                }
            }
        },
        data() {
            return {
                tree: {
                    raw: [],
                    data: [],
                    selected: {id: this.selectedId}
                },
                expandedNodeIds: new Set()
            }
        },
        methods: {
            loadTreeData() {
                let ajax = util.ajax.get(`/api/${this.domainUrl}`);
                ajax.then((response)=>{
                    response = this.treeTransformResponse(response);
                    // 如果树刷新需要重新选中之前选中的节点
                    response.data.forEach((n)=>{
                        n.selected = n.id === this.tree.selected.id;
                        n.title = n.name;
                        n.expand = this.expandedNodeIds.has(n.id);
                        if(n._addAble !== false) {
                            n._addAble = this.dataType === 'tree'
                        }
                    });
                    let data = response.data;
                    if(this.dataType === 'tree') {
                        data = util.transformTreeData(data, 'name', false);
                    }
                    this.tree.raw = response.data;
                    this.tree.data = [
                        {
                            id: null,
                            selected: this.tree.selected.id === null,
                            title: this.rootName,
                            expand: true,
                            _root: true,
                            _editAble: false,
                            _deleteAble: false,
                            children: [...data]
                        }
                    ];
                    if(this.tree.selected.id === null) {
                        this.tree.selected = this.tree.data[0];
                    }
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
                if(!id) {
                    data = this.tree.data[0];
                }
                this.tree.selected.selected = false;
                this.tree.selected = data;
                data.selected = true;
                this.$emit('on-select-change', [data]);
            },
            toggleExpand(data) {
                if(data.expand) {
                    this.expandedNodeIds.add(data.id);
                } else {
                    this.expandedNodeIds.delete(data.id);
                }
            }
        },
        mounted() {
        }
    }
</script>