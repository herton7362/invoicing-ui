<template>
    <Modal v-model="form.modal"
           :title="formTitle"
           :loading="form.loading"
           :mask-closable="maskClosable"
           @on-ok="save"
           :width="modalWidth">
        <Form ref="form" :model="form.data" :rules="formRule" :label-width="labelWidth">
            <slot name="edit-form" :data="form.data"/>
        </Form>
    </Modal>
</template>

<script>
    import util from '@/libs/util';
    export default {
        props: {
            domainUrl: String,
            modalWidth: Number,
            formTitle: {
                type: String,
                default: '窗口'
            },
            formRule: {
                type: Object,
                default() {
                    return {};
                }
            },
            formData: {
                type: Object,
                default() {
                    return {};
                }
            },
            formTransformResponse: {
                type: Function,
                default(response) {
                    return response
                }
            },
            formTransformData: {
                type: Function,
                default(data) {
                    return data
                }
            },
            maskClosable: {
                type: Boolean,
                default: true
            },
            labelWidth: {
                type: Number,
                default: 80
            }
        },
        data() {
            return {
                form: {
                    modal: false,
                    loading: true,
                    data: this.formData
                }
            }
        },
        methods: {
            save() {
                this.$refs.form.validate((valid) => {
                    if (valid) {
                        util.ajax.post(`/api/${this.domainUrl}`, this.formTransformData(this.form.data)).then(() => {
                            this.form.modal = false;
                            this.$Message.success('保存成功');
                            this.$emit('on-save-success');
                        }).catch((error) => {
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
            },
            openNewModal() {
                this.$refs.form.resetFields();
                this.$emit('on-new-modal-open');
                this.form.data.id = null;// 解决清空表单id不会删除问题
                this.form.modal = true;
                this.$nextTick(() => util.autofocusFormField(this.$refs.form));
            },
            openEditModal(row) {
                this.$emit('on-edit-modal-open');
                this.$refs.form.resetFields();
                util.ajax.get(`/api/${this.domainUrl}/${row.id}`).then((response) => {
                    response = this.formTransformResponse(response);
                    this.form.data = response.data;
                    this.$emit('on-data-loaded', this.form.data);
                    this.form.modal = true;
                    this.$nextTick(() => util.autofocusFormField(this.$refs.form));
                });
            }
        }
    }
</script>