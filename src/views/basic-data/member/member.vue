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
                <Button type="ghost" @click="form.cardType.modal=true"> <span>会员卡类型管理</span></Button>
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
        <Modal title="会员卡类型" v-model="form.cardType.modal" >
            <member-card-type></member-card-type>
        </Modal>
    </Card>
</template>

<script>
    import util from '@/libs/util';
    import SingleTable from "../../my-components/single-table/single-table";
    import MemberCard from './member-card';
    import MemberCardType from './member-card-type';

    export default {
        components: {
            SingleTable,
            MemberCard,
            MemberCardType
        },
        data() {
            const statusRender = (h, params) => {
                if(params.row.logicallyDeleted) {
                    return h('div', [
                        h('Icon', {
                            props: {
                                type: 'record',
                                color: '#ccc'
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
            }
            return {
                table: {
                    member: {
                        columns: [
                            {
                                type: 'expand',
                                width: 50,
                                render: (h, params) => h(MemberCard, {
                                    props: {
                                        member: params.row
                                    }
                                })
                            },
                            {key:'code', title:'会员编号'},
                            {key:'name', title:'会员名'},
                            {key:'mobile', title:'电话'},
                            {key:'licenseNumber', title:'证件号码'},
                            {title:'账户储值余额',align: 'right',
                                render: (h, params) => {
                                    let span = h('span', '...');
                                    util.ajax.get(`/api/member/balance/${params.row.id}`).then((response)=>{
                                        span.elm.innerHTML = response.data;
                                    })
                                    return span;
                                }},
                            {title:'积分',align: 'right',
                                render: (h, params) => {
                                    let span = h('span', '...');
                                    util.ajax.get(`/api/member/points/${params.row.id}`).then((response)=>{
                                        span.elm.innerHTML = response.data;
                                    })
                                    return span;
                                }},
                            {title:'状态', render: statusRender},
                            {title:'会员卡',align: 'center',
                                render: (h, params) => {
                                    let span = h('a', {
                                        attrs: {
                                            href: 'javascript:void(0)'
                                        },
                                        on: {
                                            click: ()=> {
                                                this.$set(this.$refs.table.$refs.table.table.data[params.index], '_expanded', true);
                                            }
                                        }
                                    }, '...');
                                    util.ajax.get(`/api/member/cardCount/${params.row.id}`).then((response)=>{
                                        span.elm.innerHTML = `${response.data} 张`;
                                    })
                                    return span;
                                }},
                            {key:'remark', title:'备注'}
                        ],
                        actions: [
                            (h, params)=> {
                                return h('Dropdown', {
                                    style: {
                                        textAlign: 'left'
                                    }
                                }, [
                                    h('a', {
                                        attrs: {
                                            href: 'javascript:void(0)'
                                        }
                                    }, [
                                        h('span', '更多'),
                                        h('Icon', {props: {type: 'arrow-down-b'}})
                                    ]),
                                    h('DropdownMenu', {
                                        slot: 'list'
                                    }, [
                                        h('DropdownItem', {
                                            nativeOn: {
                                                click:()=>{
                                                    util.ajax.post(`/api/member/enable/${params.row.id}`).then((r)=>{
                                                        this.$Message.success('启用成功');
                                                        this.$refs.table.reloadGrid();
                                                    });
                                                }
                                            }
                                        }, '启用'),
                                        h('DropdownItem', {
                                            nativeOn: {
                                                click:()=>{
                                                    util.ajax.post(`/api/member/disable/${params.row.id}`).then((r)=>{
                                                        this.$Message.success('停用成功');
                                                        this.$refs.table.reloadGrid();
                                                    });
                                                }
                                            }
                                        }, '停用')
                                    ])
                                ])
                            }
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
                    },
                    cardType: {
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