<style lang="less">
    @import './node.less';
</style>

<template>
    <span class="editable-tree-node" :class="{'editable-tree-node-root': value._root}">
        <span class="ivu-tree-title" :class="{'ivu-tree-title-selected': value.selected}" @click="onNodeClick">{{value.title}}</span>
        <span class="editable-tree-node-tools">
            <Poptip placement="right" :width="300" transfer v-if="value._addAble !== false" v-model="form.addModal">
                <a class="editable-tree-node-operator" href="javascript:void(0)" @click="newOne">
                    <Icon type="plus" :size="14"></Icon>
                </a>
                 <div slot="content">
                     <Form ref="addForm" :model="form.data" :rules="form.rule" :label-width="0">
                         <FormItem prop="parentId">
                             <RadioGroup v-model="form.data.parentId">
                                 <Radio :label="value.parentId" :disabled="value._root">同级</Radio>
                                 <Radio :label="value.id">子集</Radio>
                             </RadioGroup>
                         </FormItem>
                         <FormItem prop="name">
                             <Input v-model="form.data.name" placeholder="请输入名称" autofocus/>
                         </FormItem>
                         <Button type="primary" long @click="save('add')" :loading="form.loading">保存</Button>
                     </Form>
                 </div>
            </Poptip>
            <Poptip placement="right" :width="300" transfer v-if="value._editAble !== false" v-model="form.editModal">
                <a class="editable-tree-node-operator" href="javascript:void(0)" @click="loadOne">
                    <Icon type="edit" :size="14"></Icon>
                </a>
                <div slot="content">
                     <Form ref="editForm" :model="form.data" :rules="form.rule" :label-width="0">
                         <FormItem prop="parentId" v-if="dataType === 'tree'">
                             <Select v-model="form.data.parentId" clearable placeholder="选择上级">
                                 <Option :key="row.id" :value="row.id" v-for="row in treeData">{{row.title}}</Option>
                             </Select>
                         </FormItem>
                         <FormItem prop="name">
                             <Input v-model="form.data.name" placeholder="请输入名称" autofocus/>
                         </FormItem>
                         <Button type="primary" long @click="save('edit')" :loading="form.loading">保存</Button>
                     </Form>
                 </div>
            </Poptip>
            <Poptip title="你确认删除这条内容吗？" confirm transfer @on-ok="remove" v-if="value._deleteAble !== false">
                <a class="editable-tree-node-operator" href="javascript:void(0)">
                    <Icon type="minus" :size="14"></Icon>
                </a>
            </Poptip>

        </span>
    </span>
</template>

<script>
    import util from '@/libs/util';
    export default {
        props: {
            domainUrl: String,
            value: Object,
            dataType: {
                type: String,
                validator(val) {
                    return 'list' === val || 'tree' === val;
                },
                default: 'list'
            },
            treeData: Array
        },
        data() {
            return {
                form: {
                    loading: false,
                    addModal: false,
                    editModal: false,
                    data: {
                        id: null,
                        parentId: null,
                        name: null
                    },
                    rule: {
                        name: [
                            { required: true, message: '请填写名称', trigger: 'blur' }
                        ]
                    }
                }
            }
        },
        methods: {
            onNodeClick() {
                this.$emit('on-node-click');
            },
            save(action) {
                this.form.loading = true;
                this.$refs[action + 'Form'].validate((valid) => {
                    if (valid) {
                        util.ajax.post(`/api/${this.domainUrl}`, this.form.data).then(() => {
                            this.$Message.success('保存成功');
                            this.form[action + 'Modal'] = false;
                            this.$emit('on-save-success');
                        });
                    }
                    this.form.loading = false;
                })
            },
            newOne() {
                this.$refs.addForm.resetFields();
                this.form.data.id = null;
                if(!this.value._root) {
                    this.form.data.parentId = this.value.parentId;
                }
                setTimeout(()=>util.autofocusFormField(this.$refs.addForm, 1), 300);
            },
            loadOne() {
                this.form.data = this.value;
                setTimeout(()=>util.autofocusFormField(this.$refs.editForm), 300);
            },
            remove() {
                util.ajax.delete(`/api/${this.domainUrl}/${this.value.id}`).then(() => {
                    this.$Message.success('删除成功');
                    this.$emit('on-delete-success');
                });
            }
        }
    }
</script>