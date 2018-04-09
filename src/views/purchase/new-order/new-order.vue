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
                        <dl v-for="data in goodsSelector.data" class="checkoutcart-group ng-scope">
                            <dt class="checkoutcart-grouptitle">{{data.name}} {{data.code}}</dt>
                            <!-- ngRepeat: item in basket -->
                            <dd v-for="sku in data.skus">
                                <div class="checkoutcart-tablerow">
                                    <div class="cell itemname"
                                         :title="sku.goodsPropertyValues && sku.goodsPropertyValues.map((s)=>s.name) || '无规格'">
                                        {{sku.goodsPropertyValues &&
                                        sku.goodsPropertyValues.map((s)=>s.name).toString()
                                        || '无规格'}}
                                    </div>
                                    <div class="cell itemquantity">
                                        <InputNumber v-model="sku.count"/>
                                    </div>
                                    <div class="cell itemtotal">¥7.50</div>
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
                        <h2>
                            收货仓库
                            <a class="checkout-addaddress"
                               href="javascript:"
                               v-if="warehouses.length"
                               @click="openNewWarehouseModal">添加新仓库</a>
                        </h2>
                        <ul class="checkout-address-list"
                            v-if="warehouses.length"
                            :class="{ showmore: showMoreAddress, showfirst: !showMoreAddress }">
                            <li class="checkout-address" v-for="warehouse in warehouses">
                                <Icon class="checkout-address-icon" type="ios-location-outline"></Icon>
                                <div class="checkout-address-info">
                                    <p>{{warehouse.name}} {{warehouse.linkman}} {{warehouse.telephone}}</p>
                                    <p>{{warehouse.shippingAddress}}</p>
                                </div>
                                <div class="checkout-address-edit">
                                    <a href="javascript:" @click="openEditWarehouseModal(warehouse)">修改</a>
                                    <Poptip placement="left-start"
                                            confirm
                                            transfer
                                            title="你确认删除这条内容吗？"
                                            @on-ok="removeWarehouse(warehouse)">
                                        <a href="javascript:">×</a>
                                    </Poptip>
                                </div>
                            </li>
                            <a class="checkout-showmoreaddress" href="javascript:" v-show="!showMoreAddress && warehouses.length > 1" @click="showMoreAddress = true">
                                显示更多仓库 <Icon type="chevron-down" size="12"></Icon>
                            </a>
                            <a class="checkout-showmoreaddress" href="javascript:" v-show="showMoreAddress" @click="showMoreAddress = false">
                                收起 <Icon type="chevron-up" size="12"></Icon>
                            </a>
                        </ul>
                        <a class="checkout-noaddress"
                           href="javascript:"
                           v-if="warehouses.length === 0"
                           @click="openNewWarehouseModal">+ 添加新仓库</a>
                        <h2 class="margin-top-large">
                            供货单位
                            <a class="checkout-addsupplier"
                               href="javascript:"
                               v-if="suppliers.length"
                               @click="openNewBusinessRelatedUnitModal">添加新供货商</a>
                        </h2>
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
                                    <a href="javascript:" @click="openEditBusinessRelatedUnitModal(supplier)">修改</a>
                                    <Poptip placement="left-start"
                                            confirm
                                            transfer
                                            title="你确认删除这条内容吗？"
                                            @on-ok="removeBusinessRelatedUnit(supplier)">
                                        <a href="javascript:">×</a>
                                    </Poptip>
                                </div>
                            </li>
                            <a class="checkout-showmoresupplier" href="javascript:" v-show="!showMoreSupplier && suppliers.length > 1" @click="showMoreSupplier = true">
                                显示更多单位 <Icon type="chevron-down" size="12"></Icon>
                            </a>
                            <a class="checkout-showmoresupplier" href="javascript:" v-show="showMoreSupplier" @click="showMoreSupplier = false">
                                收起 <Icon type="chevron-up" size="12"></Icon>
                            </a>
                        </ul>
                        <a class="checkout-nosupplier"
                           href="javascript:"
                           v-if="suppliers.length === 0"
                           @click="openNewBusinessRelatedUnitModal">+ 添加新供货商</a>
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
                        <goods-selector ref="goodsSelector" multiple @on-select-change="onSelectChange"></goods-selector>
                        <div slot="footer">
                            <Button type="text" @click="goodsSelector.modal=false">取消</Button>
                        </div>
                    </Modal>
                </Card>
            </div>
        </div>
        <warehouse-form ref="warehouseForm" @on-save-success="onWarehouseSaveSuccess"></warehouse-form>
        <business-related-unit-form ref="businessRelatedUnitForm"
                                    @on-save-success="onBusinessRelatedUnitSaveSuccess"/>
    </div>
</template>

<script>
    import util from '@/libs/util';
    import GoodsSelector from '../../goods/common/goods-selector.vue'
    import WarehouseForm from '../../basic-data/warehouse/warehouse-form';
    import BusinessRelatedUnitForm from '../../basic-data/business-related-unit/business-related-unit-form.vue';

    export default {
        components: {
            GoodsSelector,
            WarehouseForm,
            BusinessRelatedUnitForm
        },
        data() {
            return {
                goodsSelector: {
                    modal: false,
                    data: []
                },
                showMoreAddress: false,
                showMoreSupplier: false,
                warehouses: [],
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
            loadWarehouses() {
                util.ajax.get('/api/warehouse').then((response)=>{
                    this.warehouses = response.data;
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
            },
            onWarehouseSaveSuccess() {
                this.loadWarehouses();
            },
            openNewWarehouseModal() {
                this.$refs.warehouseForm.openNewModal();
            },
            openEditWarehouseModal(warehouse) {
                this.$refs.warehouseForm.openEditModal(warehouse);
            },
            onBusinessRelatedUnitSaveSuccess() {
                this.loadSuppliers();
            },
            openNewBusinessRelatedUnitModal() {
                this.$refs.businessRelatedUnitForm.openNewModal();
                this.$refs.businessRelatedUnitForm.$refs.saveForm.form.data.type = 'VENDOR';
            },
            openEditBusinessRelatedUnitModal(businessRelatedUnit) {
                this.$refs.businessRelatedUnitForm.openEditModal(businessRelatedUnit);
            },
            removeBusinessRelatedUnit (row) {
                util.ajax.delete(`/api/businessRelatedUnit/${row.id}`).then(() => {
                    this.$Message.success('删除成功');
                    this.loadSuppliers();
                });
            },
            removeWarehouse (row) {
                util.ajax.delete(`/api/warehouse/${row.id}`).then(() => {
                    this.$Message.success('删除成功');
                    this.loadWarehouses();
                });
            }
        },
        mounted() {
            this.loadWarehouses();
            this.loadSuppliers();
        }
    }
</script>