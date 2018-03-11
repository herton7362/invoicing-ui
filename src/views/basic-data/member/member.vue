<style lang="less">
    @import '../../../styles/common.less';
</style>

<template>
    <Card>
        <single-table
                ref="table"
                :columns="table.member.columns"
                :actions="table.member.actions"
                form-title="会员维护"
                domain-url="member"
                :form-rule="form.member.rule"
                :form-data="form.member.data"
                :form-transform-data="memberFormTransformData"
                :form-transform-response="memberFormTransformResponse"
                :table-transform-query-params="memberTableTransformQueryParams"
                @on-new-modal-open="onMemberNewModalOpen">
            <template slot="query-form" slot-scope="props">
                <FormItem class="padding-right-medium" prop="name" label="会员名称">
                    <Input v-model="props.params.name" placeholder="会员名称"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="mobile" label="电话">
                    <Input v-model="props.params.mobile" placeholder="电话"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="code" label="会员编号">
                    <Input v-model="props.params.code" placeholder="会员编号"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="logicallyDeleted" label="会员状态">
                    <RadioGroup v-model="props.params.logicallyDeleted">
                        <Radio :label="0">启用</Radio>
                        <Radio :label="1">停用</Radio>
                        <Radio label="">所有</Radio>
                    </RadioGroup>
                </FormItem>
                <FormItem class="padding-right-medium" prop="cardNumber" label="会员卡号">
                    <Input v-model="props.params.cardNumber" placeholder="会员卡号"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="status" label="会员卡类型">
                    <Select v-model="props.params.status">
                        <Option value="">全部</Option>
                    </Select>
                </FormItem>
            </template>

            <template slot="actions">
                <Button type="ghost"> <span>会员卡类型管理</span></Button>
            </template>

            <template slot="edit-form" slot-scope="props">
                <Card dis-hover>
                    <span slot="title">
                        基本信息
                    </span>
                    <Row>
                        <Col :span="12">
                        <FormItem label="会员名" prop="name">
                            <Input v-model="props.data.name" placeholder="会员名"/>
                        </FormItem>
                        </Col>
                        <Col :span="12">
                        <FormItem label="联系电话" prop="mobile">
                            <Input v-model="props.data.mobile" placeholder="联系电话"/>
                        </FormItem>
                        </Col>
                    </Row>
                    <Row>
                        <Col :span="24">
                        <FormItem label="证件号码" prop="licenseNumber">
                            <Input v-model="props.data.licenseNumber" placeholder="证件号码"/>
                        </FormItem>
                        </Col>
                    </Row>
                    <Row>
                        <Col :span="12">
                        <FormItem label="会员状态" prop="logicallyDeleted">
                            <RadioGroup v-model="props.data.logicallyDeleted">
                                <Radio :label="0">启用</Radio>
                                <Radio :label="1">停用</Radio>
                            </RadioGroup>
                        </FormItem>
                        </Col>
                    </Row>
                    <Row>
                        <Col :span="24">
                        <FormItem label="备注" prop="remark">
                            <Input v-model="props.data.remark" placeholder="备注"/>
                        </FormItem>
                        </Col>
                    </Row>
                </Card>
                <Card dis-hover class="margin-top-medium">
                    <span slot="title">
                        账户信息
                    </span>
                    <div style="float: left">
                        <label class="ivu-form-item-label">账户储值余额（元）</label>
                        <span class="ivu-form-item-content">{{props.data.balance}}</span>
                    </div>
                    <div style="float: right">
                        <label class="ivu-form-item-label">账户积分</label>
                        <span class="ivu-form-item-content">{{props.data.points}}</span>
                    </div>
                    <div style="clear:both"></div>
                </Card>
            </template>
        </single-table>
        <Modal title="会员卡" v-model="form.card.modal" :width="900">
            <Table :columns="table.card.columns"></Table>
        </Modal>
    </Card>
</template>

<script>
    import util from '@/libs/util';
    import PagedTable from '@/views/my-components/single-table/paged-table.vue';
    import SingleTable from "../../my-components/single-table/single-table";

    export default {
        components: {
            SingleTable,
            PagedTable
        },
        data() {
            return {
                table: {
                    member: {
                        columns: [
                            {key:'code', title:'会员编号'},
                            {key:'name', title:'会员名'},
                            {key:'mobile', title:'电话'},
                            {key:'licenseNumber', title:'证件号码'},
                            {key:'balance', title:'账户储值余额',
                                render: (h, params) => {
                                    let span = h('span', '...');
                                    util.ajax.get(`/api/member/balance/${params.row.id}`).then((response)=>{
                                        span.elm.innerHTML = response.data;
                                    })
                                    return span;
                                }},
                            {key:'points', title:'积分',
                                render: (h, params) => {
                                    let span = h('span', '...');
                                    util.ajax.get(`/api/member/points/${params.row.id}`).then((response)=>{
                                        span.elm.innerHTML = response.data;
                                    })
                                    return span;
                                }},
                            {key:'logicallyDeleted', title:'状态',
                                render: (h, params) => {
                                    if(params.row.logicallyDeleted) {
                                        return h('div', [
                                            h('Icon', {
                                                props: {
                                                    type: 'record',
                                                    color: '#f5222d'
                                                },
                                                style: {
                                                    marginRight: '5px'
                                                }
                                            }),
                                            h('span', '停用')
                                        ]);
                                    } else{
                                        return h('div', [
                                            h('Icon', {
                                                props: {
                                                    type: 'record',
                                                    color: '#52c41a'
                                                },
                                                style: {
                                                    marginRight: '5px'
                                                }
                                            }),
                                            h('span', '启用')
                                        ]);
                                    }
                                }},
                            {key:'inputManner', title:'录入方式',
                                render: (h, params) => {
                                    if(params.row.inputManner === 'ARTIFICIAL') {
                                        return h('span', '手动录入');
                                    } else if(params.row.inputManner === 'EXCEL_IMPORT') {
                                        return h('span', 'excel导入');
                                    }
                                    return h('span', '未知');
                                }},
                            {key:'remark', title:'备注'}
                        ],
                        actions: [
                            (h, params) => {
                                return h('Button', {
                                    props: {
                                        type: 'ghost',
                                        size: 'small'
                                    },
                                    style: {
                                        marginRight: '5px'
                                    },
                                    on: {
                                        click: ()=> {
                                            this.form.card.modal = true;
                                        }
                                    }
                                }, '会员卡')
                            }
                        ]
                    },
                    card: {
                        columns: [
                            {key:'code', title:'会员卡号'},
                            {key:'code', title:'会员卡类型'},
                            {key:'code', title:'储值余额'},
                            {key:'code', title:'积分'},
                            {key:'code', title:'折扣'},
                            {key:'code', title:'发卡日期'},
                            {key:'code', title:'到期日期'},
                            {key:'code', title:'状态'},
                            {key:'code', title:'备注'}
                        ]
                    }
                },
                form: {
                    member: {
                        rule: {
                            name: [
                                {required: true, message: '请填写会员名', trigger: 'blur'}
                            ],
                            mobile: [
                                {required: true, message: '请填写联系电话', trigger: 'blur'},
                                {type: 'string', message: '手机格式不正确', pattern: /^[1][3,4,5,7,8][0-9]{9}$/, trigger: 'blur'}
                            ]
                        },
                        data: {
                            id: null,
                            name: null,
                            mobile: null,
                            licenseNumber: null,
                            logicallyDeleted: 0,
                            remark: null,
                            balance: 0,
                            points: 0
                        }
                    },
                    card: {
                        modal: false
                    }
                }
            }
        },
        methods: {
            onMemberNewModalOpen() {
              // 新增用户时余额默认为零
                this.form.member.data.balance = 0;
                this.form.member.data.points = 0;
            },
            memberFormTransformData(data) {
                data.inputManner = 'ARTIFICIAL';// 录入方式手动录入
                return data;
            },
            memberFormTransformResponse(response) {
                response.data.logicallyDeleted = response.data.logicallyDeleted? 1: 0;
                response.data.balance = 0;
                response.data.points = 0;
                util.ajax.get(`/api/member/balance/${response.data.id}`).then((r)=>{
                    response.data.balance = r.data;
                });
                util.ajax.get(`/api/member/points/${response.data.id}`).then((r)=>{
                    response.data.points = r.data;
                });
                return response;
            },
            memberTableTransformQueryParams(data) {
                let result = Object.assign({}, data, {
                    logicallyDeleted: data.logicallyDeleted? true: false
                });
                if(data.logicallyDeleted === undefined || data.logicallyDeleted === '') {
                    delete result.logicallyDeleted
                }
                return result;
            }
        },
        mounted() {
            this.$refs.table.loadGrid();
        }
    };
</script>