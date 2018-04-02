<style lang="less">
    @import './authorization.less';
</style>

<template>
    <Modal class="authorization" v-model="authorization.modal"
           title="角色授权"
           :loading="authorization.loading"
           @on-ok="authorize">
        <Form :label-width="80">
            <FormItem label="角色名称">
                {{authorization.name}}
                <Poptip class="delete-role" confirm transfer title="你确认删除当前角色吗？" @on-ok="remove">
                    <a href="javascript:void(0)">删除该角色</a>
                </Poptip>
            </FormItem>
            <FormItem label="选择权限">
                <div class="authorization-tree-wrapper">
                    <Tree ref="authorizationTree"
                          class="authorization-tree"
                          :data="authorization.data"
                          show-checkbox></Tree>
                </div>
            </FormItem>
        </Form>
    </Modal>
</template>

<script>
    import util from '@/libs/util';
    import IScroll from 'iscroll';
    export default {
        data() {
            return {
                authorization: {
                    id: null,
                    name: null,
                    modal: false,
                    loading: true,
                    raw: [],
                    data: [],
                    scroll: null
                }
            }
        },
        methods: {
            authorize() {
                // 将半选的权限获取到
                const addHalfCheckedNodes = (checkedNodes) => {
                    let [addedIds, halfCheckedNodes] = [[
                        ...checkedNodes.map((node)=> node.id)
                    ], []];
                    let addNode = (node)=> {
                        if(node.parent && !util.oneOf(node.parent.id, addedIds)) {
                            halfCheckedNodes.push(node.parent);
                            addedIds.push(node.parent.id);
                            addNode(node.parent);
                        }
                    };
                    checkedNodes.forEach(addNode);
                    checkedNodes.push(...halfCheckedNodes);
                };

                let checkedNodes = this.$refs.authorizationTree.getCheckedNodes();
                addHalfCheckedNodes(checkedNodes);
                util.ajax.post('/api/role/authorize', {
                    roleId: this.authorization.id,
                    moduleIds: checkedNodes.map((node)=> node.id)
                }).then(() => {
                    this.authorization.modal = false;
                    this.$Message.success('授权成功');
                });
            },
            openAuthorizeModal(row) {
                const isLeafNode = (node) => {
                    return !node.children || node.children.length === 0;
                };

                util.ajax.get(`/api/role/authorizations/${row.id}`).then((response)=>{
                    const checkedIds = response.data.map((n)=>n.id);
                    this.clearChecked();
                    // 由于iview 半选的问题所以不主动勾选父节点
                    // 只勾选最下级节点
                    this.authorization.raw.forEach((n)=>n.checked = util.oneOf(n.id, checkedIds) && isLeafNode(n));
                    this.authorization.modal = true;
                    this.authorization.id = row.id;
                    this.authorization.name = row.name;
                })
            },
            clearChecked() {
                this.authorization.raw.forEach((n)=>n.checked = false);
            },
            loadModules() {
                util.ajax.get('/api/module', {
                    params: {
                        sort:'sortNumber,updatedDate',
                        order: 'asc,desc'
                    }
                }).then((response)=>{
                    // 增加checked属性
                    this.authorization.raw = response.data.content.map((n)=>{
                        n.checked = false;
                        n.expand = true;
                        return n;
                    });
                    const nodes = util.transformTreeData(response.data.content, 'name');
                    this.authorization.data = nodes;
                });
            },
            remove () {
                util.ajax.delete(`/api/role/${this.authorization.id}`).then(() => {
                    this.$Message.success('删除成功');
                    this.authorization.modal = false;
                    this.$emit('on-delete-success');
                });
            }
        },
        mounted() {
            this.loadModules();
        }
    }
</script>