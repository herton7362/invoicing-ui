<style lang="less">
    @import '../../../styles/common.less';
    @import './employee.less';
</style>

<template>
    <div class="employee">
        <div class="employee-head">
            <Alert show-icon>根据员工的职能选择角色，然后新增账号。可以自定义角色，进行权限配置。</Alert>
            <Button type="primary">员工列表</Button>
        </div>

        <Row :gutter="24">
            <Col :sm="12" :md="8">
            <Button class="employee-add" long type="dashed" icon="ios-plus-empty" @click="openNewRoleModal">
                新增角色
            </Button>
            </Col>
            <Col :key="data.id" :sm="12" :md="8" v-for="data in role.data">
            <Card class="employee-block">
                <Avatar :icon="data.icon" size="large" />
                <div class="employee-block-detail">
                    <div class="employee-block-title">
                        {{data.name}}
                        <Button class="config" type="text" size="small">
                            <span class="text">配置权限</span> <Icon type="gear-a"></Icon>
                        </Button>
                    </div>
                    <div class="employee-block-description">
                        <div>
                            {{data.remark}}
                        </div>
                    </div>
                </div>
                <ul class="employee-actions">
                    <li style="width: 50%;">
                        <Button type="text" long>新增账号</Button>
                    </li>
                    <li style="width: 50%;">
                        <Button type="text" long>管理账号</Button>
                    </li>
                </ul>
            </Card>
            </Col>
        </Row>
        <Spin size="large" fix v-if="role.loading"></Spin>
        <Modal v-model="role.modal" title="新增角色" :loading="role.form.loading" @on-ok="saveRole" :width="400">
            <Form ref="roleForm" :model="role.form.data" :rules="role.form.rule" :label-width="80">
                <FormItem label="角色名称" prop="name">
                    <Input v-model="role.form.data.name" placeholder="请输入角色名称"/>
                </FormItem>
            </Form>
        </Modal>
    </div>

</template>

<script>
    import util from '@/libs/util';
    export default {
        data() {
            return {
                role: {
                    loading: false,
                    data: [],
                    modal: false,
                    form: {
                        loading: true,
                        data: {
                            id: null,
                            name: null,
                            icon: 'person'
                        },
                        rule: {
                            name: [
                                { required: true, message: '请填写名称', trigger: 'blur' }
                            ]
                        }
                    }
                }
            }
        },
        methods: {
            loadRoles() {
                this.role.loading = true;
                util.ajax.get('/api/role', {
                    params: {
                        sort:'sortNumber',
                        order: 'asc'
                    }
                }).then((response)=>{
                    this.role.data = response.data.content;
                    this.role.loading = false;
                })
            },
            openNewRoleModal() {
                this.$refs.roleForm.resetFields();
                this.role.form.data.id = null;// 解决清空表单id不会删除问题
                this.role.modal = true;
            },
            saveRole() {
                this.$refs.roleForm.validate((valid) => {
                    if (valid) {
                        util.ajax.post(`/api/role`, this.role.form.data).then(() => {
                            this.role.modal = false;
                            this.$Message.success('保存成功');
                            this.loadRoles();
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
                this.roleForm.loading = false;
                this.$nextTick(() => {
                    this.roleForm.loading = true;
                });
            }
        },
        mounted() {
            this.loadRoles();
        }
    }
</script>