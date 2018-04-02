<style lang="less">
    @import '../../../styles/common.less';
    @import './employee.less';
</style>

<template>
    <div class="employee">
        <div class="employee-head">
            <Alert show-icon>根据员工的职能选择角色，然后新增账号。可以自定义角色，进行权限配置。</Alert>
            <RadioGroup v-model="admin.visible" type="button" @on-change="onVisibleChange">
                <Radio :label="0">角色</Radio>
                <Radio :label="1">员工</Radio>
            </RadioGroup>
        </div>

        <Row :gutter="24" v-if="!admin.visible">
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
                        <Button class="config" type="text" size="small" @click="openAuthorizeModal(data)">
                            <span class="text">配置权限</span> <Icon type="gear-a"></Icon>
                        </Button>
                    </div>
                    <div class="employee-block-description">
                        {{data.remark}}
                    </div>
                    <p>
                        该角色目前已配置 <span class="role-staff-account-mount">{{data.staffAccountMount}}</span> 个账号
                    </p>
                </div>
                <ul class="employee-actions">
                    <li style="width: 50%;">
                        <Button type="text" long @click="openNewAdminModal(data)">新增账号</Button>
                    </li>
                    <li style="width: 50%;">
                        <Button type="text" long @click="switchAdminList(data)">管理账号</Button>
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
        <admin ref="admin"
               class="margin-top-large"
               v-show="admin.visible"
               @on-save-success="onSaveSuccess"></admin>
        <authorization ref="authorization" @on-delete-success="onAuthorizationDelete"></authorization>
    </div>

</template>

<script>
    import util from '@/libs/util';
    import Admin from '../../admin/admin/list.vue'
    import Authorization from '../../admin/role/authorization.vue';
    export default {
        components:{
            Admin,
            Authorization
        },
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
                },
                admin: {
                    visible: 0
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
            },
            onVisibleChange(val) {
                if(!val) {
                    this.loadRoles();
                } else {
                    this.$refs.admin.loadGrid();
                }
            },
            openAuthorizeModal(row) {
                this.$refs.authorization.openAuthorizeModal(row);
            },
            onAuthorizationDelete() {
                this.loadRoles();
            },
            openNewAdminModal(data) {
                this.$refs.admin.$refs.table.openNewModal();
                this.$refs.admin.$refs.table.form.data.roleIds = [data.id];
            },
            onSaveSuccess() {
                this.loadRoles();
            },
            switchAdminList(data) {
                this.$refs.admin.$refs.table.$refs.table.queryParams.roleId = data.id;
                this.admin.visible = 1;
                this.$refs.admin.loadGrid();
            }
        },
        mounted() {
            this.loadRoles();
        }
    }
</script>