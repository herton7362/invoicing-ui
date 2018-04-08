<template>
    <save-form ref="saveForm"
               domain-url="warehouse"
               form-title="仓库维护"
               :modal-width="600"
               :form-rule="form.rule"
               :form-data="form.data"
               @on-save-success="onSaveSuccess">
        <template slot="edit-form" slot-scope="props">
            <Card dis-hover>
                    <span slot="title">
                        基本信息
                    </span>
                <Row>
                    <FormItem label="仓库名称" prop="name">
                        <Input v-model="props.data.name" placeholder="仓库名称" @on-change="onNameChange"/>
                    </FormItem>
                </Row>
                <Row>
                    <Col :span="8">
                    <FormItem label="仓库编号" prop="code">
                        <Input v-model="props.data.code" placeholder="仓库编号"/>
                    </FormItem>
                    </Col>
                    <Col :span="8">
                    <FormItem label="仓库简名" prop="shortname">
                        <Input v-model="props.data.shortname" placeholder="仓库简名"/>
                    </FormItem>
                    </Col>
                    <Col :span="8">
                    <FormItem label="拼音码" prop="pinyin">
                        <Input v-model="props.data.pinyin" placeholder="拼音码"/>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <FormItem label="备注" prop="remark">
                        <Input v-model="props.data.remark" placeholder="备注"/>
                    </FormItem>
                </Row>
            </Card>

            <Card dis-hover class="margin-top-medium">
                    <span slot="title">
                        联系信息【将作为你物流单上的寄件人信息打印出来，请确保信息准确】
                    </span>
                <Row>
                    <Col :span="8">
                    <FormItem label="联系人" prop="linkman">
                        <Input v-model="props.data.linkman" placeholder="联系人"/>
                    </FormItem>
                    </Col>
                    <Col :span="8">
                    <FormItem label="联系电话" prop="telephone">
                        <Input v-model="props.data.telephone" placeholder="联系电话"/>
                    </FormItem>
                    </Col>
                    <Col :span="8">
                    <FormItem label="邮政编码" prop="zipCode">
                        <Input v-model="props.data.zipCode" placeholder="邮政编码"/>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col :span="8">
                    <FormItem label="地址" prop="addressId">
                        <Select v-model="props.data.addressId" placeholder="地址">

                        </Select>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <FormItem label="发货地址" prop="shippingAddress">
                        <Input v-model="props.data.shippingAddress" placeholder="发货地址"/>
                    </FormItem>
                </Row>
            </Card>
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
        data() {
            return {
                form: {
                    rule: {
                        name: [
                            {required: true, message: '请填写仓库名称', trigger: 'blur'}
                        ],
                        code: [
                            {required: true, message: '请填写仓库编号', trigger: 'blur'}
                        ]
                    },
                    data: {
                        id: null,
                        name: null,
                        code: null,
                        shortname: null,
                        pinyin: null,
                        remark: null,
                        linkman: null,
                        telephone: null,
                        zipCode: null,
                        addressId: null,
                        shippingAddress: null
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