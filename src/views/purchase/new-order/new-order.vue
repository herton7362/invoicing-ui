<style lang="less">
    @import './new-order.less';
</style>

<template>
    <div class="new-order">
        <div class="new-order-main checkout">
            <div class="new-order-left">
                <Card class="checkoutcart-container" :padding="0">
                    <div class="checkoutcart-title" slot="title">
                        <h2>订单详情</h2>
                    </div>
                    <a class="plus-goods" href="#" slot="extra" @click.prevent="openGoodsSelector">
                        <Icon type="ios-plus-empty"></Icon>
                        添加商品
                    </a>
                    <div>
                        <div class="checkoutcart-tablerow tablehead">
                            <div class="cell itemname">商品</div>
                            <div class="cell itemquantity">份数</div>
                            <div class="cell itemtotal">小计（元）</div>
                        </div>
                        <dl ng-if="basket.length" ng-repeat="basket in cart.vm.group" class="checkoutcart-group ng-scope">
                            <dt ng-bind="$index + 1 + '号购物车'" class="checkoutcart-grouptitle">1号购物车</dt>
                            <!-- ngRepeat: item in basket -->
                            <dd ng-repeat="item in basket" class="ng-scope">
                                <div class="checkoutcart-tablerow">
                                    <div class="cell itemname" ng-bind="item.name" title="合茶碗蒸">合茶碗蒸</div>
                                    <div class="cell itemquantity">
                                        <InputNumber />
                                    </div>
                                    <div class="cell itemtotal">¥7.50</div>
                                </div>
                            </dd><!-- end ngRepeat: item in basket -->
                            <dd ng-repeat="item in basket" class="ng-scope">
                                <div class="checkoutcart-tablerow">
                                    <div class="cell itemname" ng-bind="item.name" title="韩式泡菜">韩式泡菜</div>
                                    <div class="cell itemquantity">
                                        <InputNumber />
                                    </div>
                                    <div class="cell itemtotal">¥2.00</div>
                                </div>
                            </dd><!-- end ngRepeat: item in basket -->
                        </dl>
                        <div class="checkoutcart-total color-stress">¥<span class="num">16.50</span></div>
                        <div class="checkoutcart-totalextra">
                            共 <span>2</span> 份商品
                        </div>
                    </div>
                </Card>
            </div>
            <div class="new-order-list">
                <Card class="checkout-content" :padding="0">
                    <Form :label-width="80">
                        <h2>收货仓库 <a class="checkout-addaddress" href="javascript:" v-if="stores.length">添加新仓库</a></h2>
                        <ul class="checkout-address-list"
                            v-if="stores.length"
                            :class="{ showmore: showMoreAddress, showfirst: !showMoreAddress }">
                            <li class="checkout-address" v-for="store in stores">
                                <Icon class="checkout-address-icon" type="ios-location-outline"></Icon>
                                <div class="checkout-address-info">
                                    <p>{{store.name}} {{store.mobile}}</p>
                                    <p>{{store.detailAddress}}</p>
                                </div>
                                <div class="checkout-address-edit">
                                    <a href="javascript:">修改</a>
                                    <a href="javascript:">×</a>
                                </div>
                            </li>
                            <a class="checkout-showmoreaddress" href="javascript:" v-show="!showMoreAddress && stores.length > 1" @click="showMoreAddress = true">
                                显示更多仓库 <Icon type="chevron-down" size="12"></Icon>
                            </a>
                            <a class="checkout-showmoreaddress" href="javascript:" v-show="showMoreAddress" @click="showMoreAddress = false">
                                收起 <Icon type="chevron-up" size="12"></Icon>
                            </a>
                        </ul>
                        <a class="checkout-noaddress" href="javascript:" v-if="stores.length === 0">+ 添加新仓库</a>
                        <h2 class="margin-top-large">供货单位 <a class="checkout-addsupplier" href="javascript:" v-if="suppliers.length">添加新单位</a></h2>
                        <ul class="checkout-supplier-list"
                            v-if="suppliers.length"
                            :class="{ showmore: showMoreSupplier, showfirst: !showMoreSupplier }">
                            <li class="checkout-supplier" v-for="supplier in suppliers">
                                <Icon class="checkout-supplier-icon" type="log-in"></Icon>
                                <div class="checkout-supplier-info">
                                    <p>{{supplier.name}}</p>
                                    <p class="color-mute">{{supplier.depositBank}} {{supplier.bankAccount}}</p>
                                    <p class="color-mute">{{supplier.linkman}} {{supplier.mobile}} {{supplier.telephone}}</p>
                                </div>
                                <div class="checkout-supplier-edit">
                                    <a href="javascript:">修改</a>
                                    <a href="javascript:">×</a>
                                </div>
                            </li>
                            <a class="checkout-showmoresupplier" href="javascript:" v-show="!showMoreSupplier && suppliers.length > 1" @click="showMoreSupplier = true">
                                显示更多单位 <Icon type="chevron-down" size="12"></Icon>
                            </a>
                            <a class="checkout-showmoresupplier" href="javascript:" v-show="showMoreSupplier" @click="showMoreSupplier = false">
                                收起 <Icon type="chevron-up" size="12"></Icon>
                            </a>
                        </ul>
                        <a class="checkout-nosupplier" href="javascript:" v-if="suppliers.length === 0">+ 添加新供货商</a>
                        <h2 class="margin-top-large">其他信息</h2>
                        <FormItem label="经手人">
                            <Select placeholder="请选择经手人" style="width: 150px;">

                            </Select>
                        </FormItem>
                        <FormItem label="交货日期">
                            <DatePicker type="date" placeholder="请选择交货日期" style="width: 150px"></DatePicker>
                        </FormItem>
                        <FormItem label="摘要">
                            <Input placeholder="请输入摘要" style="width: 300px;"/>
                        </FormItem>
                        <FormItem label="附加说明">
                            <Input type="textarea" :rows="4" placeholder="请输入附加说明" style="width: 300px;"/>
                        </FormItem>
                        <FormItem>
                            <Button class="margin-top-medium" type="error" size="large" style="padding: 8px 30px;">确认新增</Button>
                        </FormItem>
                    </Form>

                    <Modal title="选择商品" v-model="goodsSelector.modal" :width="800">
                        <goods-selector multiple @on-select-change="onSelectChange"></goods-selector>
                        <div slot="footer">
                            <Button type="text" @click="goodsSelector.modal=false">取消</Button>
                        </div>
                    </Modal>
                </Card>
            </div>
        </div>
    </div>
</template>

<script>
    import util from '@/libs/util';
    import GoodsSelector from '../../goods/common/goods-selector.vue'
    export default {
        components: {
            GoodsSelector
        },
        data() {
            return {
                goodsSelector: {
                    modal: false,
                    data: [],
                    columns: [
                        {render:(h, {row}) => {
                                const tags = [];
                                if(row.goodsPropertyValues) {
                                    row.goodsPropertyValues.forEach((goodsPropertyValue)=>{
                                        tags.push(h('Tag', {
                                            props: {
                                                color: 'blue'
                                            }
                                        }, goodsPropertyValue.name))
                                    });
                                }
                                return h('div', [
                                    h('p', `商品名称：${row.goods.name}`),
                                    h('p', `商品编码：${row.goods.code}`),
                                    ...tags
                                ])
                            }},
                        {render:(h, {row}) => {
                                return h('div', [
                                    h('div', [
                                        h('label', {class: 'new-order-title'}, '重量：'),
                                        h('span', `${row.goods.weight} kg`)
                                    ]),
                                    h('div', [
                                        h('label', {class: 'new-order-title'}, '长度：'),
                                        h('span', row.goods.length)
                                    ]),
                                    h('div', [
                                        h('label', {class: 'new-order-title'}, '宽度：'),
                                        h('span', row.goods.width)
                                    ]),
                                    h('div', [
                                        h('label', {class: 'new-order-title'}, '高度：'),
                                        h('span', row.goods.height)
                                    ])
                                ])
                            }},
                        {render:(h, {row}) => {
                                return h('div', [
                                    h('p', `库存：10 ${row.goods.basicGoodsPrice.unitName?row.goods.basicGoodsPrice.unitName: ''}`),
                                    h('p', [h('a', {
                                        attrs: {
                                            href: 'javascript:void(0)'
                                        }
                                    }, '查看库存')])
                                ])
                            }}
                    ]
                },
                showMoreAddress: false,
                showMoreSupplier: false,
                stores: [],
                suppliers: []
            }
        },
        methods: {
            openGoodsSelector() {
                this.goodsSelector.modal = true;
            },
            onSelectChange(datas) {
                this.goodsSelector.data = datas;
            },
            loadStores() {
                util.ajax.get('/api/store').then((response)=>{
                    this.stores = response.data;
                })
            },
            loadSuppliers() {
                util.ajax.get('/api/businessRelatedUnit', {
                    params: {
                        type: 'VENDOR'
                    }
                }).then((response)=>{
                    this.suppliers = response.data;
                })
            }
        },
        mounted() {
            this.loadStores();
            this.loadSuppliers();
        }
    }
</script>