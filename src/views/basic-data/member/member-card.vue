<style lang="less">
    @import '../../../styles/common.less';
    @import 'member-card.less';
</style>

<template>
    <div class="member-card">
        <Spin size="large" fix v-if="loading"></Spin>
        <Card dis-hover>
            <Row><Button type="dashed" long icon="plus-round" @click="openNewModal"><span>新建会员卡</span></Button></Row>
            <Row class="margin-top-medium">
                <div class="member-card-list">
                    <div class="member-card-summary" v-for="row in data">
                        <div class="capital">
                            <div class="balance">
                                <div class="mini-counts">{{row.balance}}</div>
                                <div style="font-size: 11px">余额</div>
                            </div>
                            <div class="points">
                                <div class="mini-counts">{{row.points}}</div>
                                <div>积分</div>
                            </div>
                            <div class="discount">
                                <div class="mini-counts">{{row.discount}}</div>
                                <div>折扣</div>
                            </div>
                        </div>
                        <div class="card"><Icon type="card"></Icon></div>
                        <div class="summary">
                            <div class="card-type">{{row.memberCardTypeName}}</div>
                            <div class="card-number">{{row.cardNumber}}</div>
                        </div>
                        <div class="end">
                            <div class="actions">
                                <a href="javascript:void(0)" @click="openEditModal(row)">修改</a>
                                <Poptip confirm transfer title="您确定删除这条内容吗？" @on-ok="remove(row)">
                                    <a href="javascript:void(0)">删除</a>
                                </Poptip>
                                <Dropdown :transfer="true">
                                    <a href="javascript:void(0)">更多 <Icon type="arrow-down-b"></Icon></a>
                                    <DropdownMenu slot="list">
                                        <DropdownItem>启用</DropdownItem>
                                        <DropdownItem>停用</DropdownItem>
                                        <DropdownItem @click.native="openRechargeModal(row)">会员卡充值</DropdownItem>
                                        <DropdownItem>积分兑换</DropdownItem>
                                        <DropdownItem>积分调整</DropdownItem>
                                    </DropdownMenu>
                                </Dropdown>
                            </div>
                            <div class="expire-date">
                                <span v-html="formatExpireDate(row.expireDate)"></span>
                                <Tooltip :content="`有效期 ${row.createdDate} 至 ${row.expireDate}`">
                                    <Icon type="help-circled"></Icon>
                                </Tooltip>
                            </div>
                        </div>
                    </div>
                </div>
            </Row>
        </Card>
        <Modal v-model="form.modal"
               title="会员卡维护"
               :loading="form.loading"
               :mask-closable="false"
               @on-ok="save">
            <Form ref="form" :model="form.data" :rules="form.rule" :label-width="100">
                <Row>
                    <FormItem label="会员卡类型" prop="memberCardTypeId">
                        <RadioGroup v-model="form.data.memberCardTypeId" @on-change="memberCardTypeIdChange">
                            <Radio :label="memberCardType.id"
                                   v-for="memberCardType in memberCardTypes"
                                   :key="memberCardType.id">{{memberCardType.name}}</Radio>
                        </RadioGroup>
                    </FormItem>
                </Row>
                <Row>
                    <Col :span="12">
                    <FormItem label="会员卡号" prop="cardNumber">
                        <Input v-model="form.data.cardNumber" placeholder="会员卡号"/>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col :span="12">
                    <FormItem label="有效期（年）" prop="validDate">
                        <InputNumber v-model="form.data.validDate" placeholder="有效期（年）" @on-change="validDateChange"/>
                    </FormItem>
                    </Col>
                    <Col :span="12">
                    <FormItem label="会员卡到期日" prop="expireDate">
                        <DatePicker type="date" v-model="form.data.expireDate" placeholder="会员卡到期日期" @on-change="expireDateChange"/>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col :span="12">
                    <FormItem label="会员卡" prop="logicallyDeleted">
                        <RadioGroup v-model="form.data.logicallyDeleted">
                            <Radio :label="0">启用</Radio>
                            <Radio :label="1">停用</Radio>
                        </RadioGroup>
                    </FormItem>
                    </Col>
                    <Col :span="12">
                    <FormItem label="折扣" prop="discount">
                        <InputNumber v-model="form.data.discount" placeholder="0.9为9折"/>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col :span="12">
                    <FormItem label="初始积分" prop="points">
                        <InputNumber v-model="form.data.points" placeholder="初始积分"/>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col :span="12">
                    <FormItem label="账户消费密码" prop="passwordForShow">
                        <Input v-model="form.data.passwordForShow" placeholder="账户消费密码" disabled/>
                    </FormItem>
                    </Col>
                    <Col :span="12">
                    <a class="margin-left-small margin-top-small"
                       href="javascript:void(0)"
                       style="display: inline-block"
                       @click="setPassword">设置密码</a>
                    <a class="margin-left-small margin-top-small"
                       href="javascript:void(0)"
                       style="display: inline-block"
                       @click="clearPassword">清空密码</a>
                    </Col>
                </Row>
                <Row>
                    <FormItem label="备注" prop="remark">
                        <Input v-model="form.data.remark" placeholder="备注"/>
                    </FormItem>
                </Row>
            </Form>

            <Modal :transfer="false"
                   title="设置新密码"
                   v-model="form.password.modal"
                   :loading="form.password.loading"
                   :width="400"
                   @on-ok="saveNewPassword">
                <Form ref="passwordForm" :model="form.password.data" :rules="form.password.rule" :label-width="100">
                    <FormItem label="新密码" prop="password">
                        <Input type="password" v-model="form.password.data.password" placeholder="新密码">
                        <span slot="append">
                            <Tooltip content="密码为6位数字">
                                <Icon type="help"></Icon>
                            </Tooltip>
                        </span>
                        </Input>
                    </FormItem>
                    <FormItem label="再次确认" prop="confirmPassword">
                        <Input type="password" v-model="form.password.data.confirmPassword" placeholder="再次确认"/>
                    </FormItem>
                </Form>
            </Modal>
        </Modal>

        <Modal title="会员充值"
               v-model="form.recharge.modal"
               :loading="form.recharge.loading"
               @on-ok="recharge">
            <Form ref="rechargeForm" :model="form.recharge.data" :rules="form.recharge.rule" :label-width="120">
                <Card dis-hover>
                    <div slot="title">
                          <Row>
                              <Col :span="12">
                              {{form.recharge.data.memberCardTypeName}} ({{form.recharge.data.cardNumber}})
                              </Col>
                              <Col :span="12">
                              <div class="account-name">{{member.name}} {{member.mobile}}</div>
                              </Col>
                          </Row>
                    </div>
                    <Row>
                        <div class="last-balance">
                            <Icon type="social-yen"></Icon> 当前余额:{{form.recharge.data.balance}}元
                        </div>
                    </Row>
                </Card>
                <Card dis-hover class="margin-top-medium">
                    <Row>
                        <FormItem label="本次充值（元）">
                            <InputNumber placeholder="本次充值（元）" style="width: 100%"/>
                        </FormItem>
                    </Row>
                    <Row>
                        <FormItem label="本次赠送（元）">
                            <InputNumber placeholder="本次赠送（元）" style="width: 100%"/>
                        </FormItem>
                    </Row>
                    <Row>
                        <FormItem label="收款账户">
                            <Input placeholder="收款账户"/>
                        </FormItem>
                    </Row>
                    <Row>
                        <FormItem label="备注">
                            <Input placeholder="备注"/>
                        </FormItem>
                    </Row>
                </Card>
            </Form>
        </Modal>
    </div>
</template>

<script>
    import util from '@/libs/util';
    export default {
        name: 'member-card',
        props: {
            member: Object,
        },
        data() {
            return {
                loading: true,
                data: [],
                form: {
                    modal: false,
                    loading: true,
                    data: {
                        id: null,
                        memberId: null,
                        memberCardTypeId: null,
                        cardNumber: null,
                        validDate: 1,
                        expireDate: util.dateFormat(new Date(new Date().getTime() + 1000*60*60*24*30*12)),
                        logicallyDeleted: 0,
                        discount: null,
                        points: 0,
                        balance: 0,
                        passwordForShow: null,
                        password: null,
                        remark: null
                    },
                    rule: {
                        memberCardTypeId: [
                            { required: true, message: '请选择会员卡类型', trigger: 'change' }
                        ],
                        cardNumber: [
                            { required: true, message: '请填写会员卡号', trigger: 'blur' }
                        ],
                        discount: [
                            { required: true, message: '请填写折扣', trigger: 'blur', type: 'number' },
                            { type: 'number', message: '必须为大于0小于1的小数', min: 0, max: 1, trigger: 'blur'}
                        ]
                    },
                    password: {
                        modal: false,
                        loading: true,
                        data: {
                            password: null,
                            confirmPassword: null
                        },
                        rule: {
                            password: [
                                { required: true, message: '请填写密码', trigger: 'blur' },
                                { type: 'string', message: '密码为6位数字', pattern: /^[0-9]{6}$/, trigger: 'blur'}
                            ],
                            confirmPassword: [
                                {validator: (rule, value, callback, source, options) => {
                                    var errors = [];
                                    if(this.form.password.data.password !== value) {
                                        errors.push('两次密码输入不一致')
                                    }
                                    callback(errors);
                                }}
                            ]
                        }
                    },
                    recharge: {
                        modal: false,
                        loading: true,
                        data: {

                        },
                        rule: {

                        }
                    }
                },
                memberCardTypes: []
            }
        },
        methods: {
            loadMemberCardType() {
                return util.ajax.get('/api/memberCardType', {
                    params: {
                        sort: 'discount',
                        order: 'desc'
                    }
                }) .then((response) => {
                    this.memberCardTypes = response.data.content;
                })
            },
            formatExpireDate(val) {
                let months = 1000*60*60*24*30;
                let now = new Date().getTime();
                let expire = util.dateToLong(val);
                if(now > expire) {
                    return '已过期';
                } else if(expire - now < months) {
                    return '<span style="color: #f54545">即将到期</span>';
                }
                return val;
            },
            openNewModal(){
                this.$refs.form.resetFields();
                this.form.data.id = null;// 解决清空表单id不会删除问题
                // 选中折扣最高的卡
                let memberCardType = {discount:0};
                this.memberCardTypes.forEach((d)=>{
                    if(d.discount > memberCardType.discount) {
                        memberCardType = d;
                    }
                });
                this.form.data.discount = memberCardType.discount;
                this.form.data.memberCardTypeId = memberCardType.id;
                this.form.modal = true;
            },
            openEditModal(row) {
                this.$refs.form.resetFields();
                util.ajax.get(`/api/memberCard/${row.id}`).then((response) => {
                    response.data.logicallyDeleted = response.data.logicallyDeleted? 1: 0;
                    this.form.data = response.data;
                    this.form.modal = true;
                });
            },
            loadData(member) {
                this.loading = true;
                this.form.data.memberId = member.id;
                util.ajax.get('/api/memberCard', {
                    params: {
                        sort: 'sortNumber,updatedDate',
                        order: 'asc,desc',
                        memberId: member.id
                    }
                }) .then((response) => {
                    response.data.content.forEach((data)=>{
                        data.memberCardTypeName = this.memberCardTypes.find((d)=>d.id === data.memberCardTypeId).name
                    })
                    this.data = response.data.content;
                    this.loading = false;
                })
            },
            save() {
                this.$refs.form.validate((valid) => {
                    if (valid) {
                        util.ajax.post(`/api/memberCard`, this.form.data).then(() => {
                            this.form.modal = false;
                            this.$Message.success('保存成功');
                            this.loadData(this.member);
                        }).catch((error)=>{
                            this.clearFormLoading();
                            return Promise.reject(error);
                        });
                    } else {
                        this.clearFormLoading(this.form);
                    }
                })
            },
            remove(row) {
                util.ajax.delete(`/api/memberCard/${row.id}`).then(() => {
                    this.$Message.success('删除成功');
                    this.loadData(this.member);
                });
            },
            clearFormLoading(form) {
                form.loading = false;
                this.$nextTick(() => {
                    form.loading = true;
                });
            },
            memberCardTypeIdChange(val) {
                this.form.data.discount = this.memberCardTypes.find((d)=>d.id === val).discount;
            },
            validDateChange(val) {
                if(val === null || val === undefined || val < 0) {
                    this.$nextTick(() => {
                        this.form.data.validDate = 0;
                    });
                    val = 0;
                }
                this.form.data.expireDate = util.dateFormat(new Date(new Date().getTime() + 1000*60*60*24*30*12*val));
            },
            expireDateChange(val) {
                this.form.data.validDate = new Number(((new Date(val).getTime() - new Date().getTime()) / (1000*60*60*24*30*12)).toFixed(1));
            },
            setPassword() {
                this.$refs.passwordForm.resetFields();
                this.form.password.modal = true;
            },
            clearPassword() {
                this.form.data.password = null;
            },
            saveNewPassword() {
                this.$refs.passwordForm.validate((valid) => {
                    if (valid) {
                        this.form.data.password = this.form.password.data.password;
                        this.form.password.modal = false;
                    } else {
                        this.clearFormLoading(this.form.password);
                    }
                })
            },
            openRechargeModal(row) {
                util.ajax.get(`/api/memberCard/${row.id}`).then((response) => {
                    response.data.memberCardTypeName = this.memberCardTypes.find((d)=>d.id === response.data.memberCardTypeId).name
                    this.form.recharge.data = response.data;
                    this.form.recharge.modal = true;
                });
            },
            recharge() {

            }
        },
        mounted() {
            this.loadMemberCardType().then(()=>this.loadData(this.member));
        },
        watch: {
            'form.data.password'(val) {
                if(val) {
                    this.form.data.passwordForShow = 'xxxxxxxxxxxxxxxxxx';
                } else {
                    this.$set(this.form.data, 'passwordForShow', null);
                    this.$refs.passwordForm.resetFields();
                    this.form.password.modal = true;
                    this.form.password.modal = false;
                }
            }
        }
    }
</script>