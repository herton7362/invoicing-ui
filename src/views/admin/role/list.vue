<style lang="less">
    @import '../../../styles/common.less';
</style>

<template>
    <Card>
        <single-table ref="table"
                      :columns="table.columns"
                      :actions="table.actions"
                      form-title="角色维护"
                      domain-url="role"
                      :form-rule="form.rule"
                      :form-data="form.data">
            <template slot="query-form" slot-scope="props">
                <FormItem class="padding-right-medium" prop="name" label="登录名">
                    <Input v-model="props.params.name" placeholder="登录名"/>
                </FormItem>
            </template>

            <template slot="edit-form" slot-scope="props">
                <FormItem label="名称" prop="name">
                    <Input v-model="props.data.name" placeholder="请输入名称"/>
                </FormItem>
            </template>
        </single-table>
        <authorization ref="authorization"></authorization>
    </Card>
</template>

<script>
import util from '@/libs/util';
import singleTable from '@/views/my-components/single-table/single-table.vue';
import Authorization from './authorization.vue';

export default {
    components: {
        singleTable,
        Authorization
    },
    data() {
        return {
            table: {
                columns: [
                    {key:'name', title:'名称'}
                ],
                actions: [
                    (h, params)=> {
                        return h('a', {
                            attr: {
                                href: 'javascript:void(0)'
                            },
                            style: {
                                marginRight: '5px'
                            },
                            on: {
                                click: () => {
                                    this.openAuthorizeModal(params.row);
                                }
                            }
                        }, '授权')
                    }
                ]
            },
            form: {
                rule: {
                    name: [
                        { required: true, message: '请填写角色名', trigger: 'blur' }
                    ]
                },
                data: {
                    id: null,
                    name: null
                }
            }
        }
    },
    methods: {
        openAuthorizeModal(row) {
            this.$refs.authorization.openAuthorizeModal(row);
        }
    },
    mounted() {
        this.$refs.table.loadGrid();
    }
};
</script>
