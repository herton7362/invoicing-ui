<style lang="less">
    @import '../../../styles/common.less';
</style>

<template>
    <Card>
        <single-table ref="table"
                      :columns="table.columns"
                      form-title="门店维护"
                      domain-url="store"
                      :modal-width="700"
                      :label-width="90"
                      :form-rule="form.rule"
                      :form-data="form.data">
            <template slot="query-form" slot-scope="props">
                <FormItem class="padding-right-medium" prop="name" label="门店名称">
                    <Input v-model="props.params.name" placeholder="门店名称"/>
                </FormItem>
            </template>

            <template slot="edit-form" slot-scope="props">
                <Tabs value="basic">
                    <TabPane label="基本信息" name="basic">
                        <Row>
                            <Col :span="8">
                            <FormItem label="门店名称" prop="name">
                                <Input v-model="props.data.name" placeholder="门店名称"/>
                            </FormItem>
                            </Col>
                            <Col :span="8">
                            <FormItem label="门店编码" prop="code">
                                <Input v-model="props.data.code" placeholder="门店编码"/>
                            </FormItem>
                            </Col>
                            <Col :span="8">
                            <FormItem label="选择仓库" prop="warehouseId">
                                <Select v-model="props.data.warehouseId" placeholder="选择仓库">
                                    <Option v-for="warehouse in warehouses" :value="warehouse.id" :key="warehouse.id">
                                        {{warehouse.name}}
                                    </Option>
                                </Select>
                            </FormItem>
                            </Col>
                        </Row>
                        <Row>
                            <Col :span="8">
                            <FormItem label="开始营业时间" prop="startBusinessTime">
                                <TimePicker type="time" v-model="props.data.startBusinessTime" placeholder="选择开始营业时间"></TimePicker>
                            </FormItem>
                            </Col>
                            <Col :span="8">
                            <FormItem label="关闭营业时间" prop="closeBusinessTime">
                                <TimePicker type="time" v-model="props.data.closeBusinessTime" placeholder="选择关闭营业时间"></TimePicker>
                            </FormItem>
                            </Col>
                        </Row>
                        <Row>
                            <FormItem label="门店介绍" prop="description">
                                <Input v-model="props.data.description" placeholder="门店介绍"/>
                            </FormItem>
                        </Row>
                        <Row>
                            <Col :span="8">
                            <FormItem label="门店状态" prop="logicallyDeleted">
                                <RadioGroup v-model="props.data.logicallyDeleted">
                                    <Radio :label="0">启用</Radio>
                                    <Radio :label="1">停用</Radio>
                                </RadioGroup>
                            </FormItem>
                            </Col>
                            <Col :span="8">
                            <FormItem label="店长" prop="storeManagerId">
                                <Select v-model="props.data.storeManagerId" placeholder="选择店长">
                                </Select>
                            </FormItem>
                            </Col>
                        </Row>
                    </TabPane>
                    <TabPane label="门店地址" name="address">
                        <Row>
                            <Col :span="8">
                            <FormItem label="地址" prop="addressId">
                                <Select v-model="props.data.addressId" placeholder="选择地址">
                                </Select>
                            </FormItem>
                            </Col>
                            <Col :span="8">
                            <FormItem label="邮编" prop="zipCode">
                                <Input v-model="props.data.zipCode" placeholder="邮编"/>
                            </FormItem>
                            </Col>
                        </Row>
                        <Row>
                            <FormItem label="详细地址" prop="detailAddress">
                                <Input v-model="props.data.detailAddress" placeholder="详细地址"/>
                            </FormItem>
                        </Row>
                    </TabPane>
                    <TabPane label="门店联系方式" name="contact-type">
                        <Row>
                            <Col :span="8">
                            <FormItem label="联系人" prop="linkman">
                                <Input v-model="props.data.linkman" placeholder="联系人"/>
                            </FormItem>
                            </Col>
                            <Col :span="8">
                            <FormItem label="手机" prop="mobile">
                                <Input v-model="props.data.mobile" placeholder="手机"/>
                            </FormItem>
                            </Col>
                            <Col :span="8">
                            <FormItem label="固定电话" prop="telephone">
                                <Input v-model="props.data.telephone" placeholder="固定电话"/>
                            </FormItem>
                            </Col>
                        </Row>
                        <Row>
                            <Col :span="8">
                            <FormItem label="传真" prop="fax">
                                <Input v-model="props.data.fax" placeholder="传真"/>
                            </FormItem>
                            </Col>
                        </Row>
                        <Row>
                            <FormItem label="备注" prop="remark">
                                <Input v-model="props.data.remark" placeholder="备注"/>
                            </FormItem>
                        </Row>
                    </TabPane>
                </Tabs>
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
                        {key: 'name', title: '门店名称'},
                        {title: '店长'},
                        {key: 'startBusinessTime', title: '开始营业时间'},
                        {key: 'closeBusinessTime', title: '关闭营业时间'},
                        {title: '门店状态', render:(h, params) => {
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
                        {key: 'linkman', title: '联系人'},
                        {key: 'code', title: '门店编码'}
                    ]
                },
                form: {
                    rule: {
                        name: [
                            {required: true, message: '请填写门店名称', trigger: 'blur'}
                        ],
                        code: [
                            {required: true, message: '请填写门店编号', trigger: 'blur'}
                        ]
                    },
                    data: {
                        id: null,
                        name: null,
                        code: null,
                        warehouseId: null,
                        startBusinessTime: null,
                        closeBusinessTime: null,
                        description: null,
                        storeManagerId: null,
                        addressId: null,
                        zipCode: null,
                        detailAddress: null,
                        linkman: null,
                        mobile: null,
                        telephone: null,
                        fax: null,
                        remark: null
                    }
                },
                warehouses: []
            }
        },
        methods: {
            loadWarehouses() {
                util.ajax.get('/api/warehouse').then((response)=>{
                    this.warehouses = response.data;
                })
            }
        },
        mounted() {
            this.$refs.table.loadGrid();
            this.loadWarehouses();
        }
    };
</script>