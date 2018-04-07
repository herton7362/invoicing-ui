<style lang="less">
    @import '../../../styles/common.less';
</style>

<template>
    <Card>
        <paged-table ref="table"
                      :columns="table.columns"
                      form-title="商品sku维护"
                      domain-url="goodsSku">
            <template slot="query-form" slot-scope="props">
                <FormItem class="padding-right-medium" prop="goodsId" label="商品">
                    <Select v-model="props.params.goodsId" placeholder="选择商品">
                        <Option v-for="goods in goodses" :value="goods.id" :key="goods.id">{{goods.name}}</Option>
                    </Select>
                </FormItem>
                <FormItem class="padding-right-medium" prop="barcode" label="条码">
                    <Input v-model="props.params.barcode" placeholder="条码"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="code" label="sku码">
                    <Input v-model="props.params.code" placeholder="sku码"/>
                </FormItem>
            </template>
        </paged-table>
    </Card>
</template>

<script>
    import util from '@/libs/util';
    import PagedTable from '@/views/my-components/single-table/paged-table.vue';

    export default {
        components: {
            PagedTable
        },
        data() {
            return {
                table: {
                    columns: [
                        {title:'商品', width: 200, render: (h, {row}) => row.goods.name},
                        {
                            title:'属性值',
                            render: (h, params) => {
                                const tags = [];
                                if(params.row.goodsPropertyValues) {
                                    params.row.goodsPropertyValues.forEach((goodsPropertyValue)=>{
                                        tags.push(h('Tag', {
                                            props: {
                                                color: 'blue'
                                            }
                                        }, goodsPropertyValue.name))
                                    });
                                    return h('div', tags);
                                }
                               return '无';
                            }
                        },
                        {
                            title: '条码',
                            width: 200,
                            render: (h, params) => {
                                return h('Input', {
                                    props: {
                                        value: params.row.barcode
                                    },
                                    on: {
                                        input: (val)=> {
                                            params.row.barcode = val;
                                        },
                                        'on-blur': () => {
                                            this.edit(params.row);
                                        }
                                    }
                                });
                            }
                        },
                        {
                            title: 'sku码',
                            width: 200,
                            render: (h, params) => {
                                return h('Input', {
                                    props: {
                                        value: params.row.code
                                    },
                                    on: {
                                        input: (val)=> {
                                            params.row.code = val;
                                        },
                                        'on-blur': () => {
                                            this.edit(params.row);
                                        }
                                    }
                                });
                            }
                        }
                    ]
                },
                goodses: []
            }
        },
        methods: {
            loadGoodses() {
                util.ajax.get('/api/goods', {
                    params: {
                        sort: 'sortNumber,updatedDate',
                        order: 'asc,desc'
                    }
                }) .then((response) => {
                    this.goodses = response.data.content;
                })
            },
            loadGrid() {
                this.$refs.table.loadGrid();
            },
            edit(row) {
                util.ajax.post(`/api/goodsSku`, row).then(() => {
                    this.$Message.success('保存成功');
                });
            }
        },
        mounted() {
            this.loadGoodses();
            this.loadGrid();
        }
    };
</script>