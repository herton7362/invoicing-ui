<style lang="less">
</style>

<template>
    <Modal class="property-selector"
           v-model="form.modal"
           title="属性组维护"
           :loading="form.loading"
           @on-ok="save">
        <Form ref="form" :model="form.data" :rules="form.rule" :label-width="80">
            <Row>
                <Col :span="12">
                <FormItem label="名称" prop="name">
                    <Input v-model="form.data.name" placeholder="请输入名称"/>
                </FormItem>
                </Col>
                <Col :span="12">
                <FormItem label="备注" prop="remark">
                    <Input v-model="form.data.remark" placeholder="请输入备注"/>
                </FormItem>
                </Col>
            </Row>
            <property-selector :default-selected-data="defaultSelectedData"
                               @on-select-change="onSelectChange"></property-selector>
        </Form>
    </Modal>
</template>

<script>
    import util from '@/libs/util';
    import PropertySelector from './property-selector.vue';
    export default {
        components: {
            PropertySelector
        },
        data() {
            return {
                form: {
                    modal: false,
                    loading: true,
                    data: {
                        id: null,
                        name: null,
                        remark: null,
                        goodsPropertyGroupProperties: []
                    },
                    rule: {
                        name: [
                            { required: true, message: '请填写属性组名称', trigger: 'blur' }
                        ]
                    }
                },
                defaultSelectedData: {}
            }
        },
        methods: {
            openNewModal() {
                this.$refs.form.resetFields();
                this.form.data.id = null;// 解决清空表单id不会删除问题
                this.defaultSelectedData = {};
                this.form.modal = true;
                this.$nextTick(()=>util.autofocusFormField(this.$refs.form));
            },
            openEditModal(row) {
                this.$refs.form.resetFields();
                this.defaultSelectedData = {};
                util.ajax.get(`/api/goodsPropertyGroup/${row.id}`).then((response)=>{
                    this.form.data = response.data;
                    const defaultSelectedData = {};
                    response.data.goodsPropertyResults.forEach((result)=>{
                        const goodsPropertyGroupProperties = [];
                        result.goodsPropertyValueResults.forEach((value)=>{
                            goodsPropertyGroupProperties.push(value.id);
                        });
                        defaultSelectedData[result.id] = goodsPropertyGroupProperties;
                    });
                    this.defaultSelectedData = defaultSelectedData;
                    this.form.modal = true;
                    this.$nextTick(()=>util.autofocusFormField(this.$refs.form));
                });
            },
            onSelectChange(data) {
                const goodsPropertyGroupProperties = [];
                for(let key in data) {
                    if(!data[key]) {
                        continue;
                    }
                    const goodsPropertyGroupPropertyValues = [];
                    data[key].forEach((d)=>{
                        goodsPropertyGroupPropertyValues.push({
                            goodsPropertyValueId: d
                        });
                    })
                    goodsPropertyGroupProperties.push({
                        goodsPropertyId: key,
                        goodsPropertyGroupPropertyValues: goodsPropertyGroupPropertyValues
                    });
                }
                this.form.data.goodsPropertyGroupProperties = goodsPropertyGroupProperties;
            },
            save() {
                this.$refs.form.validate((valid) => {
                    if (valid) {
                        util.ajax.post(`/api/goodsPropertyGroup`, this.form.data).then(() => {
                            this.form.modal = false;
                            this.$Message.success('保存成功');
                            this.$emit('on-save-success');
                        }).catch((error)=>{
                            this.clearFormLoading();
                            return Promise.reject(error);
                        });
                    } else {
                        this.clearFormLoading();
                    }
                })
            },
            clearFormLoading() {
                this.form.loading = false;
                this.$nextTick(() => {
                    this.form.loading = true;
                });
            }
        },
        mounted() {
        }
    }
</script>