<style lang="less">
    @import './node.less';
</style>

<template>
    <span class="editable-tree-node" :class="{'editable-tree-node-root': value._root}">
        <span class="ivu-tree-title" :class="{'ivu-tree-title-selected': value.selected}" @click="onNodeClick">{{value.title}}</span>
        <span class="editable-tree-node-tools">
            <Poptip placement="right" :width="300" transfer v-if="dataType !== 'list' || value._root" v-model="form.modal">
                <a class="editable-tree-node-operator" href="javascript:void(0)" @click="newOne">
                    <Icon type="plus" :size="14"></Icon>
                </a>
                 <div slot="content">
                     <Form ref="form" :model="form.data" :rules="form.rule" :label-width="0">
                         <FormItem prop="parentId">
                             <RadioGroup v-model="form.data.parentId">
                                 <Radio :label="value.parentId" :disabled="value._root">同级</Radio>
                                 <Radio :label="value.id">子集</Radio>
                             </RadioGroup>
                         </FormItem>
                         <FormItem prop="name">
                             <Input v-model="form.data.name" placeholder="请输入名称"/>
                         </FormItem>
                         <Button type="primary" long @click="save" :loading="form.loading">保存</Button>
                     </Form>
                 </div>
            </Poptip>
            <Poptip placement="right" :width="300" transfer v-if="!value._root" v-model="form.modal">
                <a class="editable-tree-node-operator" href="javascript:void(0)" @click="loadOne">
                    <Icon type="edit" :size="14"></Icon>
                </a>
                <div slot="content">
                     <Form ref="form" :model="form.data" :rules="form.rule" :label-width="0">
                         <FormItem prop="parentId">
                             <Select v-model="form.data.parentId" placeholder="选择上级">
                                 <Option :value="row.id" v-for="row in treeData">{{row.title}}</Option>
                             </Select>
                         </FormItem>
                         <FormItem prop="name">
                             <Input v-model="form.data.name" placeholder="请输入名称"/>
                         </FormItem>
                         <Button type="primary" long @click="save" :loading="form.loading">保存</Button>
                     </Form>
                 </div>
            </Poptip>
            <Poptip title="您确认删除这条内容吗？" confirm transfer @on-ok="remove" v-if="!value._root">
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
                    modal: false,
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
            save() {
                this.form.loading = true;
                this.$refs.form.validate((valid) => {
                    if (valid) {
                        util.ajax.post(`/api/${this.domainUrl}`, this.form.data).then(() => {
                            this.$Message.success('保存成功');
                            this.form.modal = false;
                            this.$emit('on-save-success');
                        });
                    }
                    this.form.loading = false;
                })
            },
            newOne() {
                this.$refs.form.resetFields();
                this.form.data.id = null;
            },
            loadOne() {
                this.form.data = this.value;
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