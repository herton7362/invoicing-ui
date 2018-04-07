<style lang="less">
    @import '../../../styles/common.less';
    @import './goods-selector.less';
</style>

<template>
    <div class="goods-selector">
        <Card dis-hover>
            <Row class="search-row">
                <label class="margin-right-medium">所属类目</label>
                <Tag closable
                     color="blue"
                     @on-close="onRemoveSelectedCategory(goodsCategoryId)"
                     :key="goodsCategoryId"
                     v-for="(goodsCategoryId, index) in queryParams.goodsCategoryId">
                    {{selectedGoodsCategories[index].name}}
                </Tag>
                <Poptip placement="right-start" v-model="categoryAddModal">
                    <Button type="dashed" icon="plus" size="small">添加</Button>
                    <div slot="content">
                        <Tree :data="goodsCategories" @on-select-change="onSelectCategory"></Tree>
                    </div>
                </Poptip>
            </Row>
            <Row>
                <label class="margin-right-medium">快速查询</label>
                <Input style="width: 150px;" placeholder="请输入查询条件"/>
            </Row>
        </Card>

        <Row :gutter="16">
            <Col :key="goods.id" :span="12" v-for="goods in goodses">
                <Card dis-hover class="goods-card" :padding="0">
                    <Row>
                        <div style="float: left">
                            <img width="100"
                                 height="100"
                                 v-if="goods.goodsCoverImage.attachmentId"
                                 :src="goods.goodsCoverImage.attachmentId | attachment">
                        </div>
                        <div class="margin-left-small" style="float: left; width: 250px">
                            <h3 class="goods-name ui-ellipsis">
                                <Tooltip :content="goods.name" placement="top">{{goods.name}}</Tooltip>
                            </h3>
                            <p class="goods-code">{{goods.code}}</p>
                            <span class="goods-store-left">余量 10</span>
                        </div>
                        <Poptip class="select-button" placement="right-start" @on-popper-show="onSelectGoods(goods)">
                            <Button type="primary" shape="circle" size="small">选中</Button>
                            <goods-sku-multi-selector v-if="multiple"
                                                      :ref="`goodsSkuSelector-${goods.id}`"
                                                      :goods="goods"
                                                      :selected-skus="selectedSkus"
                                                      slot="content"
                                                      @on-select-change="onSelectChange"></goods-sku-multi-selector>
                        </Poptip>

                    </Row>
                </Card>
            </Col>
        </Row>
        <Row class="margin-top-medium" style="text-align: center">
            <Page :total="total"
                  size="small"
                  show-total
                  @on-change="onPageChange"
                  @on-page-size-change="onPageSizeChange"></Page>
        </Row>
    </div>
</template>
<script>
    import util from '@/libs/util';
    import GoodsSkuMultiSelector from './goods-sku-multi-selector.vue';

    export default {
        props: {
            multiple: Boolean
        },
        components: {
            GoodsSkuMultiSelector
        },
        data() {
            return {
                queryParams: {
                    goodsCategoryId: []
                },
                total: 0,
                currentPage: 1,
                pageSize: 10,
                selectedGoodsCategories: [],
                goodses: [],
                goodsCategories: [],
                categoryAddModal: false,
                selectedSkus: []
            }
        },
        methods: {
            onPageChange(page) {
                this.currentPage = page;
                this.loadGoodses();
            },
            onPageSizeChange(pageSize) {
                this.pageSize = pageSize;
                this.loadGoodses();
            },
            loadGoodses() {
                util.ajax.get('/api/goods', {
                    params: {
                        currentPage: this.currentPage,
                        pageSize: this.pageSize
                    }
                }).then((response)=>{
                    this.goodses = response.data.content;
                    this.total = response.data.totalElements;
                })
            },
            loadGoodsCategories() {
                util.ajax.get(`/api/goodsCategory`).then((response)=>{
                    // 如果树刷新需要重新选中之前选中的节点
                    response.data.forEach((n)=>{
                        n.title = n.name;
                    });
                    this.goodsCategories = util.transformTreeData(response.data, 'name');
                });
            },
            onSelectCategory(data) {
                if(data.length && !this.queryParams.goodsCategoryId.includes(data[0].id)) {
                    this.queryParams.goodsCategoryId.push(data[0].id)
                    this.selectedGoodsCategories.push(data[0]);
                }
                this.categoryAddModal = false;
            },
            onRemoveSelectedCategory(id) {
                const index = this.queryParams.goodsCategoryId.indexOf(id);
                this.queryParams.goodsCategoryId.splice(index, 1);
                this.selectedGoodsCategories.splice(index, 1);
            },
            onSelectGoods(goods) {
                this.$refs[`goodsSkuSelector-${goods.id}`][0].loadGoodsSkus(goods);
            },
            onSelectChange(datas) {
                this.$emit('on-select-change', datas);
            }
        },
        mounted() {
            this.loadGoodses();
            this.loadGoodsCategories();
        },
        filters: {
            attachment(val) {
                if(val) {
                    return util.baseURL + '/attachment/download/' + val;
                } else {
                    return util.baseURL + '/attachment/download/none-cover';
                }
            }
        }
    }
</script>