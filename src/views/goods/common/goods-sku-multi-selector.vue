<style lang="less">
    @import '../../../styles/common.less';
</style>

<template>
    <div :id="`scroll-content-${goods.id}`" class="ivu-poptip-body-content-inner">
        <Spin size="large" fix v-if="goodsSku.loading"></Spin>
        <div>
            <div :key="sku.id" class="margin-bottom-small goods-sku-item" v-for="sku in goodsSku.data">
                <template v-for="value in sku.goodsPropertyValues">
                    {{value.name}}&nbsp;
                </template>
                <stepper v-model="sku.count" :min="0" size="small"/>
            </div>
        </div>
    </div>
</template>

<script>
    import util from '@/libs/util';
    import IScroll from 'iscroll';
    import Stepper from '@/views/my-components/stepper/stepper.vue';

    export default {
        components: {
            Stepper
        },
        props: {
            goods: Object,
            selectedSkus: Array
        },
        data() {
            return {
                goodsSku: {
                    data: [],
                    loading:false
                },
                unwatchList: []
            }
        },
        methods: {
            loadGoodsSkus(goods) {
                this.goodsSku.loading = true;
                this.goodsSku.data = [];
                util.ajax.get(`/api/goodsSku`, {
                    params: {
                        sort: 'sortNumber',
                        order: 'asc',
                        goodsId: goods.id
                    }
                }).then((response)=>{
                    response.data.content.forEach((data)=>{
                        let s = this.selectedSkus.find((s)=>data.goodsId === s.id);
                        if(s){
                            let subSku = s.skus.find((k)=>k.id === data.id);
                            if(subSku) {
                                data.count = subSku.count;
                            } else {
                                data.count = 0;
                            }
                        } else {
                            data.count = 0;
                        }
                    });
                    this.goodsSku.data = response.data.content;
                    this.unwatchSkuCount();
                    this.watchSkuCount();
                    this.goodsSku.loading = false;
                    if(!this.goodsSku['scrll' + goods.id]) {
                        setTimeout(()=>{
                            this.loadIScroll(goods);
                        }, 500)
                    }
                })
            },
            unwatchSkuCount() {
                this.unwatchList.forEach((l)=>l());
                this.unwatchList.splice(0);
            },
            watchSkuCount() {
                this.goodsSku.data.forEach((d)=>{
                    this.unwatchList.push(this.$watch(function () {
                        return d.count;
                    }, ()=>this.onSkuChange(d)))
                })
            },
            loadIScroll(goods) {
                this.goodsSku['scrll' + goods.id] = new IScroll(`#scroll-content-${goods.id}`, {
                    mouseWheel: true,
                    scrollbars: true,
                    interactiveScrollbars: true,
                    shrinkScrollbars: 'scale',
                    fadeScrollbars: true
                });
            },
            onSkuChange(sku) {
                if(sku.count > 0) {
                    this.addSku(sku);
                } else {
                    this.removeSku(sku);
                }
                this.$emit('on-select-change', this.selectedSkus);
            },
            addSku(sku) {
                let s = this.selectedSkus.find((s)=>sku.goodsId === s.id);
                if(s) {
                    let subSku = s.skus.find((k)=>k.id === sku.id);
                    if(subSku) {
                        subSku.count = sku.count;
                    } else {
                        s.skus.push(sku);
                    }
                } else {
                    this.selectedSkus.push(Object.assign({}, sku.goods, {
                        skus: [sku]
                    }));
                }
            },
            removeSku(sku) {
                let s = this.selectedSkus.find((s)=>sku.goodsId === s.id);
                if(s) {
                    let subSku = s.skus.find((k)=>k.id === sku.id);
                    if(subSku) {
                        s.skus.splice(s.skus.findIndex((s)=>s.id === sku.id), 1);
                        if(s.skus.length === 0) {
                            this.selectedSkus.splice(this.selectedSkus.findIndex((s)=>sku.goodsId === s.id), 1);
                        }
                    }
                }
            }
        },
        beforeDestroy() {
            this.unwatchSkuCount();
        }
    }
</script>