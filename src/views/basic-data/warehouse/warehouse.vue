<style lang="less">
    @import '../../../styles/common.less';
</style>

<template>
    <Card>
        <single-table ref="table"
                      :columns="table.columns"
                      form-title="仓库维护"
                      domain-url="warehouse"
                      :modal-width="600"
                      :label-width="70"
                      :form-rule="form.rule"
                      :form-data="form.data">
            <template slot="query-form" slot-scope="props">
                <FormItem class="padding-right-medium" prop="code" label="仓库编号">
                    <Input v-model="props.params.code" placeholder="仓库编号"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="name" label="仓库名称">
                    <Input v-model="props.params.name" placeholder="仓库名称"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="shortname" label="仓库简名">
                    <Input v-model="props.params.shortname" placeholder="仓库简名"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="pinyin" label="拼音码">
                    <Input v-model="props.params.pinyin" placeholder="拼音码"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="logicallyDeleted" label="状态">
                    <RadioGroup v-model="props.params.logicallyDeleted">
                        <Radio :label="0">启用</Radio>
                        <Radio :label="1">停用</Radio>
                        <Radio label="">所有</Radio>
                    </RadioGroup>
                </FormItem>
                <FormItem class="padding-right-medium" prop="remark" label="备注">
                    <Input v-model="props.params.remark" placeholder="备注"/>
                </FormItem>
            </template>

            <template slot="edit-form" slot-scope="props">
                <Card dis-hover>
                    <span slot="title">
                        基本信息
                    </span>
                    <Row>
                        <FormItem label="仓库名称" prop="name">
                            <Input v-model="props.data.name" placeholder="仓库名称" @on-blur="onNameBlur"/>
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
                        联系信息【将作为您物流单上的寄件人信息打印出来，请确保信息准确】
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
                        {key: 'code', title: '仓库编号'},
                        {key: 'name', title: '仓库名称'},
                        {key: 'shortname', title: '仓库简名'},
                        {key: 'pinyin', title: '拼音码'},
                        {title: '状态', render:(h, params) => {
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
                            }},
                        {key: 'remark', title: '备注'}
                    ]
                },
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
            onNameBlur() {
                let name = this.form.data.name;
                if(name) {
                    if(!this.form.data.shortname) {
                        this.form.data.shortname = name;
                    }
                    if(!this.form.data.pinyin) {
                        this.form.data.pinyin = util.getFirstPinyinLetter(name);
                    }
                }
            }
        },
        mounted() {
            this.$refs.table.loadGrid();
        }
    };
</script>