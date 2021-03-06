<style lang="less">
    @import '../../../styles/common.less';
</style>

<template>
    <Card>
        <single-table ref="table"
                      :columns="table.columns"
                      form-title="管理员维护"
                      domain-url="admin"
                      :form-rule="form.rule"
                      :form-data="form.data"
                      :form-transform-response="formTransformResponse"
                      @on-new-modal-open="onNewModalOpen"
                      @on-save-success="onSaveSuccess">
            <template slot="query-form" slot-scope="props">
                <FormItem class="padding-right-medium" prop="loginName" label="登录名">
                    <Input v-model="props.params.loginName" placeholder="登录名"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="name" label="姓名">
                    <Input v-model="props.params.name" placeholder="姓名"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="roleId" label="角色">
                    <label>
                        <Select v-model="props.params.roleId" transfer>
                            <Option v-for="role in roles" :value="role.id" :key="role.id">{{role.name}}</Option>
                        </Select>
                    </label>
                </FormItem>
            </template>

            <template slot="edit-form" slot-scope="props">
                <FormItem label="登录名" prop="loginName">
                    <Input v-model="props.data.loginName" placeholder="请输入登录名" style="width: 200px;"/>
                    @{{appId}}
                </FormItem>
                <FormItem label="名称" prop="name">
                    <Input v-model="props.data.name" placeholder="请输入名称"/>
                </FormItem>
                <FormItem label="密码" prop="password">
                    <Input v-model="props.data.password" type="password" placeholder="密码为空则不修改"/>
                </FormItem>
                <FormItem label="角色" prop="roleIds" type="array">
                    <CheckboxGroup v-model="props.data.roleIds">
                        <Checkbox :label="role.id" v-for="role in roles" :key="role.id">
                            {{role.name}}
                        </Checkbox>
                    </CheckboxGroup>
                </FormItem>
            </template>
        </single-table>
    </Card>
</template>

<script>
import util from '@/libs/util';
import SingleTable from '@/views/my-components/single-table/single-table.vue';

export default {
    components: {
        SingleTable
    },
    data() {
        return {
            table: {
                columns: [
                    {key:'loginName', title:'登录名'},
                    {key:'name', title:'姓名'},
                    {
                        key:'roles',
                        title:'角色',
                        render: (h, params) => {
                            const tags = [];
                            params.row.roles.forEach((role)=>{
                                tags.push(h('Tag', {
                                    props: {
                                        color: 'green'
                                    }
                                }, role.name))
                            });
                            return h('div', tags);
                        }
                    }
                ]
            },
            form: {
                rule: {
                    loginName: [
                        { required: true, message: '请填写登录名', trigger: 'blur' }
                    ],
                    name: [
                        { required: true, message: '请填写名称', trigger: 'blur' }
                    ],
                    roleIds: [
                        { type:'array', required: true, message: '请选择角色', trigger: 'change' }
                    ]
                },
                data: {
                    id: null,
                    loginName: null,
                    name: null,
                    password: null,
                    roleIds: []
                }
            },
            roles: []
        }
    },
    methods: {
        loadRole() {
            util.ajax.get('/api/role', {
                params: {
                    sort: 'sortNumber,updatedDate',
                    order: 'asc,desc'
                }
            }) .then((response) => {
                this.roles = response.data.content;
            })
        },
        formTransformResponse(response) {
            response.data.password = null;
            return response;
        },
        loadGrid() {
            this.$refs.table.loadGrid();
        },
        onSaveSuccess() {
            this.$emit('on-save-success');
        },
        onNewModalOpen() {
            this.$refs.table.form.data.roleIds = [this.$refs.table.$refs.table.queryParams.roleId];
        }
    },
    mounted() {
        this.loadRole();
        this.loadGrid();
    },
    computed: {
        appId() {
            return this.$store.state.user.loginUser.clientId;
        }
    }
};
</script>