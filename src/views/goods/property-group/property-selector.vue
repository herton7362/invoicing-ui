<style lang="less">
    @import './property-selector.less';
</style>

<template>
    <div class="property-selector">
        <FormItem label="属性">
            <CheckboxGroup v-model="selectedGoodsPropertyIds">
                <Checkbox :key="goodsProperty.id"
                          :label="goodsProperty.id"
                          v-for="goodsProperty in goodsProperties">{{goodsProperty.name}}</Checkbox>
            </CheckboxGroup>
        </FormItem>
        <FormItem :class="{'property-group-edit-category-expanded': !hideMoreCategory}"
                  :key="goodsProperty.id"
                  :label="goodsProperty.name + '属性'"
                  v-for="goodsProperty in selectedGoodsProperties">
            <Tag :ref="goodsProperty.id + 'isNull_tag'"
                 size="small"
                 checkable
                 :checked="ifTagChecked(goodsProperty.id, 'isNull')"
                 type="border"
                 @click.native="checkTag(goodsProperty.id, 'isNull')">未分组</Tag>
            <Tag :ref="goodsProperty.id + propertyCategory.id + '_tag'"
                 size="small"
                 :key="propertyCategory.id"
                 checkable
                 :checked="ifTagChecked(goodsProperty.id, propertyCategory.id)"
                 @click.native="checkTag(goodsProperty.id, propertyCategory.id)"
                 type="border"
                 v-show="index < 3 || !hideMoreCategory"
                 v-for="(propertyCategory, index) in goodsProperty.goodsPropertyCategories">{{propertyCategory.name}}</Tag>
            <a href="javascript:void(0)"
               class="expand-handler"
               v-if="goodsProperty.goodsPropertyCategories && goodsProperty.goodsPropertyCategories.length > 3"
               @click="hideMoreCategory = !hideMoreCategory">
                {{hideMoreCategory? '展开': '收起'}} <Icon type="chevron-down"></Icon>
            </a>
            <CheckboxGroup v-model="selectedGoodsPropertyValueIds[goodsProperty.id]">
                <Checkbox :key="propertyValue.id"
                          :label="propertyValue.id"
                          v-for="propertyValue in getPropertyValuesBySelectedCategory(goodsProperty.id, goodsProperty.goodsPropertyValues)">
                    {{propertyValue.name}}
                </Checkbox>
            </CheckboxGroup>
        </FormItem>
    </div>
</template>

<script>
    import util from '@/libs/util';

    export default {
        props: {
            defaultSelectedData: {
                type: Object,
                default() {
                    return {}
                }
            }
        },
        data() {
            return {
                selectedGoodsPropertyIds: [],
                selectedGoodsProperties: [],
                selectedGoodsPropertyValueIds: {},
                selectedGoodsPropertyCategoryIds: {},
                goodsProperties: [],
                hideMoreCategory: true
            }
        },
        methods: {
            loadProperties() {
                util.ajax.get('/api/goodsProperty').then((response)=>{
                    this.goodsProperties = response.data;
                });
            },
            ifTagChecked(id, goodsPropertyCategoryId) {
                if(this.selectedGoodsPropertyCategoryIds[id]) {
                    return this.selectedGoodsPropertyCategoryIds[id].includes(goodsPropertyCategoryId);
                }
                return false;
            },
            checkTag(id, goodsPropertyCategoryId) {
                if(this.$refs[id + goodsPropertyCategoryId + '_tag'][0].isChecked) {
                    this.addPropertyValueQueryParam(id, goodsPropertyCategoryId);
                } else {
                    this.removePropertyValueQueryParam(id, goodsPropertyCategoryId);
                }
            },
            addPropertyValueQueryParam(id, goodsPropertyCategoryId) {
                const array = this.selectedGoodsPropertyCategoryIds[id];
                if(!array.includes(goodsPropertyCategoryId)) {
                    array.push(goodsPropertyCategoryId)
                }
            },
            removePropertyValueQueryParam(id, goodsPropertyCategoryId) {
                const array = this.selectedGoodsPropertyCategoryIds[id];
                if(array.includes(goodsPropertyCategoryId)) {
                    array.splice(array.findIndex((a)=>a === goodsPropertyCategoryId), 1);
                }
            },
            getPropertyValuesBySelectedCategory(id, goodsPropertyValues) {
                const array = this.selectedGoodsPropertyCategoryIds[id];
                return goodsPropertyValues.filter((g)=>{
                    return array.some((a)=>{
                        if(a === 'isNull') {
                            return g.goodsPropertyCategoryId == null;
                        }
                        return g.goodsPropertyCategoryId.split(',').includes(a);
                    });
                })
            }
        },
        mounted() {
            this.loadProperties();
        },
        watch: {
            selectedGoodsPropertyIds: {
                handler(val, oldVal) {
                    this.selectedGoodsProperties = this.goodsProperties.filter((g)=>val.includes(g.id));
                    oldVal.filter((v)=> !val.includes(v)).forEach((v)=>{
                        this.$set(this.selectedGoodsPropertyValueIds, v, null);
                        this.$set(this.selectedGoodsPropertyCategoryIds, v, null);
                    });
                    val.filter((v)=> !oldVal.includes(v)).forEach((v)=>{
                        this.$set(this.selectedGoodsPropertyValueIds, v, []);
                        this.$set(this.selectedGoodsPropertyCategoryIds, v, []);
                    });
                },
                deep: true
            },
            selectedGoodsPropertyValueIds: {
                handler(val) {
                    this.$emit('on-select-change', val);
                },
                deep: true
            },
            defaultSelectedData: {
                handler(val) {
                    this.selectedGoodsPropertyIds = Object.keys(val);
                    this.$nextTick(()=>{
                        this.selectedGoodsPropertyValueIds = Object.assign({}, val);
                        for(let k in val) {
                            this.goodsProperties.find((g)=>g.id === k).goodsPropertyValues.forEach((v)=>{
                                if(val[k].includes(v.id)) {
                                    let goodsPropertyCategoryId = v.goodsPropertyCategoryId? v.goodsPropertyCategoryId: 'isNull';
                                    this.$refs[k + goodsPropertyCategoryId + '_tag'][0].isChecked = true;
                                    this.addPropertyValueQueryParam(k, goodsPropertyCategoryId);
                                }
                            })
                        }
                    })
                },
                deep: true
            }
        }
    }
</script>