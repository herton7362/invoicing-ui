<template>
    <save-form ref="saveForm"
               domain-url="businessRelatedUnit"
               form-title="往来单位维护"
               :modal-width="800"
               :form-rule="form.rule"
               :form-data="form.data"
               :form-transform-response="tableTransformResponse"
               @on-save-success="onSaveSuccess">
        <template slot="edit-form" slot-scope="props">
            <FormItem label="单位名称" prop="name">
                <Input v-model="props.data.name" placeholder="请输入单位名称" @on-change="onNameChange"/>
            </FormItem>
            <Row>
                <Col :span="8">
                <FormItem label="单位编号" prop="code">
                    <Input v-model="props.data.code" placeholder="请输入单位编号"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="单位简名" prop="shortname">
                    <Input v-model="props.data.shortname" placeholder="请输入单位简名"/>
                </FormItem>
                </Col>
            </Row>
            <Row>
                <Col :span="8">
                <FormItem label="拼音码" prop="pinyin">
                    <Input v-model="props.data.pinyin" placeholder="请输入拼音码"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="类型" prop="type">
                    <RadioGroup v-model="props.data.type">
                        <Radio label="VENDOR"><Icon type="log-in"></Icon> 供货商</Radio>
                        <Radio label="CUSTOMER"><Icon type="person-stalker"></Icon> 客户</Radio>
                    </RadioGroup>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="税号" prop="taxNumber">
                    <Input v-model="props.data.taxNumber" placeholder="请输入税号"/>
                </FormItem>
                </Col>
            </Row>
            <Row>
                <Col :span="8">
                <FormItem label="税率(%)" prop="taxRate">
                    <InputNumber v-model="props.data.taxRate" placeholder="请输入税率(%)"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="联系人" prop="linkman">
                    <Input v-model="props.data.linkman" placeholder="请输入联系人"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="电话" prop="telephone">
                    <Input v-model="props.data.telephone" placeholder="请输入电话"/>
                </FormItem>
                </Col>
            </Row>
            <Row>
                <Col :span="8">
                <FormItem label="手机" prop="mobile">
                    <Input v-model="props.data.mobile" placeholder="请输入手机"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="电子邮件" prop="email">
                    <Input v-model="props.data.email" placeholder="请输入电子邮件"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="价格等级" prop="priceLevel">
                    <Input v-model="props.data.priceLevel" placeholder="请输入价格等级"/>
                </FormItem>
                </Col>
            </Row>
            <FormItem label="地址" prop="detailAddress">
                <Input v-model="props.data.detailAddress" placeholder="请输入地址"/>
            </FormItem>
            <Row>
                <Col :span="16">
                <FormItem label="开户银行" prop="depositBank">
                    <Input v-model="props.data.depositBank" placeholder="请输入开户银行"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="银行账号" prop="bankAccount">
                    <Input v-model="props.data.bankAccount" placeholder="请输入银行账号"/>
                </FormItem>
                </Col>
            </Row>
            <FormItem label="备注" prop="remark">
                <Input v-model="props.data.remark" placeholder="请输入备注"/>
            </FormItem>
        </template>
    </save-form>
</template>

<script>
    import util from '@/libs/util';
    import SaveForm from '@/views/my-components/single-table/save-form.vue';
    export default {
        name: 'single-table',
        components: {
            SaveForm
        },
        props: {
            tableTransformResponse: {
                type: Function,
                default(response) {
                    return response
                }
            }
        },
        data() {
            return {
                form: {
                    rule: {
                        name: [
                            { required: true, message: '请填写单位名称', trigger: 'blur' }
                        ],
                        code: [
                            { required: true, message: '请填写单位编码', trigger: 'blur' }
                        ],
                        pinyin: [
                            { required: true, message: '请填写拼音码', trigger: 'blur' }
                        ],
                        type: [
                            { required: true, message: '请选择类型', trigger: 'blur' }
                        ]
                    },
                    data: {
                        id: null,
                        name: null,
                        code: null,
                        shortname: null,
                        pinyin: null,
                        type: null,
                        taxNumber: null,
                        taxRate: 0,
                        linkman: null,
                        telephone: null,
                        mobile: null,
                        email: null,
                        priceLevel: null,
                        detailAddress: null,
                        depositBank: null,
                        bankAccount: null,
                        remark: null
                    }
                }
            }
        },
        methods: {
            openNewModal() {
                this.$refs.saveForm.openNewModal();
            },
            openEditModal(row) {
                this.$refs.saveForm.openEditModal(row);
            },
            onSaveSuccess() {
                this.$emit('on-save-success');
            },
            onNameChange() {
                const formData = this.$refs.saveForm.form.data;
                let name = formData.name;
                if(name) {
                    formData.shortname = name;
                    formData.pinyin = util.getFirstPinyinLetter(name);
                }
            }
        }
    }
</script>