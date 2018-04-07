<template>
    <div :id="`scroll-content-${goods.id}`" class="ivu-poptip-body-content-inner">
        <Spin size="large" fix v-if="goodsSku.loading"></Spin>
        <div>
            <div :key="sku.id" class="margin-bottom-small goods-sku-item" v-for="sku in goodsSku.data">
                <template v-for="value in sku.goodsPropertyValues">
                    {{value.name}}&nbsp;
                </template>
                <InputNumber v-model="sku.count" @on-blur="onSkuBlur(sku)" :min="0" size="small"/>
            </div>
        </div>
    </div>
</template>

<script>
    import util from '@/libs/util';
    import IScroll from 'iscroll';

    export default {
        props: {
            goods: Object,
            selectedSkus: Array
        },
        data() {
            return {
                goodsSku: {
                    data: [],
                    loading:false
                }
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
                        let s = this.selectedSkus.find((s)=>data.id === s.id);
                        if(s){
                            data.count = s.count;
                        } else {
                            data.count = 0;
                        }
                    })
                    this.goodsSku.data = response.data.content;
                    this.goodsSku.loading = false;
                    if(!this.goodsSku['scrll' + goods.id]) {
                        setTimeout(()=>{
                            this.loadIScroll(goods);
                        }, 500)
                    }
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
            onSkuBlur(sku) {
                let s = this.selectedSkus.find((s)=>sku.id === s.id);
                if(sku.count > 0) {
                    if(s) {
                        s.count = sku.count;
                    } else {
                        this.selectedSkus.push(sku);
                    }
                } else {
                    if(s) {
                        this.selectedSkus.splice(this.selectedSkus.findIndex((s)=>sku.id === s.id), 1);
                    }
                }
                this.$emit('on-select-change', this.selectedSkus);
            }
        }
    }
</script>