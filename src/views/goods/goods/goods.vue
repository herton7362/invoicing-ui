<style lang="less">

</style>

<template>
    <Card>
        <single-table ref="table"
                      :columns="table.columns"
                      form-title="商品维护"
                      domain-url="goods"
                      :form-rule="form.rule"
                      :form-data="form.data"
                      :modal-width="700">

            <template slot="edit-form" slot-scope="props">
                <Tabs type="card">
                    <TabPane label="基本信息">
                        <basic-form-content :form-data="props.data"></basic-form-content>
                    </TabPane>
                    <TabPane label="售价管理" style="padding: 0 20px;">
                        <sale-price-form-content :form-data="props.data"></sale-price-form-content>
                    </TabPane>
                    <TabPane label="属性设置">
                        <property-form-content :form-data="props.data"></property-form-content>
                    </TabPane>
                    <TabPane label="商品图片管理">
                        <image-form-content :form-data="props.data"></image-form-content>
                    </TabPane>
                </Tabs>
            </template>
        </single-table>
    </Card>
</template>

<script>
    import util from '@/libs/util';
    import SingleTable from '@/views/my-components/single-table/single-table.vue';
    import BasicFormContent from './basic-form-content.vue';
    import SalePriceFormContent from './sale-price-form-content.vue';
    import PropertyFormContent from './property-form-content.vue';
    import ImageFormContent from './image-form-content.vue';

    export default {
        components: {
            PropertyFormContent,
            SingleTable,
            BasicFormContent,
            SalePriceFormContent,
            ImageFormContent
        },
        data() {
            return {
                table: {
                    columns: [
                    ]
                },
                form: {
                    rule: {
                    },
                    data: {
                        id: null,
                        name: null,
                        code: null,
                        shortname: null,
                        pinyin: null,
                        brandId: null,
                        madeAddress: null,
                        standard: null,
                        model: null,
                        weight: 0,
                        length: 0,
                        width: 0,
                        height: 0,
                        costPrice: 0,
                        remark: null,
                        goodsPropertyGroupId: null,
                        basicGoodsPriceParam: {
                            id: null,
                            unitName: null,
                            conversionRelationship: null,
                            retailPrice: null
                        },
                        assistGoodsPriceParam1: {
                            id: null,
                            unitName: null,
                            conversionRelationship: null,
                            retailPrice: null
                        },
                        assistGoodsPriceParam2: {
                            id: null,
                            unitName: null,
                            conversionRelationship: null,
                            retailPrice: null
                        },
                        goodsImageParams: []
                    }
                }
            }
        },
        methods: {

        },
        mounted() {
            this.$refs.table.loadGrid();
        }
    }
</script>