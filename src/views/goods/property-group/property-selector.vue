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
                 :checked="false"
                 type="border"
                 @click.native="checkTag(goodsProperty.id, 'isNull')">未分组</Tag>
            <Tag :ref="goodsProperty.id + propertyCategory.id + '_tag'"
                 size="small"
                 :key="propertyCategory.id"
                 checkable
                 :checked="false"
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
                    <Poptip confirm transfer title="你确认删除这个属性吗？" @on-ok="remove(propertyValue)">
                        <a class="property-selector-remove" href="javascript:void(0)">
                            <Icon type="backspace" size="14"></Icon>
                        </a>
                    </Poptip>
                </Checkbox>
                <Poptip placement="bottom" :width="200" transfer v-model="goodsProperty.modal">
                    <Button icon="ios-plus-empty" type="dashed" size="small" @click="openNewPropertyValue(goodsProperty)">添加属性值</Button>
                    <div slot="content">
                        <Form :ref="goodsProperty.id + '_form'" :model="form.data" :rules="form.rule" :label-width="0">
                            <FormItem prop="name">
                                <Input v-model="form.data.name" placeholder="请输入属性值名称" autofocus/>
                            </FormItem>
                            <FormItem prop="name">
                                <Input v-model="form.data.barcode" placeholder="请输入条码"/>
                            </FormItem>
                            <FormItem prop="name">
                                <Input v-model="form.data.remark" placeholder="请输入备注"/>
                            </FormItem>
                            <Button type="primary" long @click="add(goodsProperty)" :loading="goodsProperty.loading">保存</Button>
                        </Form>
                    </div>
                </Poptip>
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
                hideMoreCategory: true,
                form: {
                    modal: false,
                    data: {
                        goodsPropertyId: null,
                        name: null,
                        barcode: null,
                        remark: null,
                        goodsPropertyCategoryId: null
                    },
                    rule: {
                        name: [
                            { required: true, message: '请填写名称', trigger: 'blur' }
                        ]
                    }
                }
            }
        },
        methods: {
            loadProperties() {
                util.ajax.get('/api/goodsProperty').then((response)=>{
                    this.goodsProperties = response.data;
                    this.selectGoodsProperty();
                });
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
            },
            remove(propertyValue) {
                util.ajax.delete(`/api/goodsPropertyValue/${propertyValue.id}`).then(()=>{
                    this.$Message.success('删除成功');
                    this.loadProperties();
                });
            },
            selectGoodsProperty() {
                this.selectedGoodsProperties = this.goodsProperties.filter((g)=>this.selectedGoodsPropertyIds.includes(g.id));
                this.$nextTick(()=>{
                    this.selectedGoodsProperties.forEach((g)=>{
                        if(g.goodsPropertyCategories.length === 0 && this.$refs[g.id + 'isNull_tag']) {
                            this.$refs[g.id + 'isNull_tag'][0].isChecked = true;
                            this.addPropertyValueQueryParam(g.id, 'isNull');
                        }
                    })
                })
            },
            openNewPropertyValue(goodsProperty) {
                this.$refs[goodsProperty.id + '_form'][0].resetFields();
                this.$nextTick(()=>util.autofocusFormField(this.$refs[goodsProperty.id + '_form'][0]));
            },
            add(goodsProperty) {
                let categoryIds = this.selectedGoodsPropertyCategoryIds[goodsProperty.id].filter((a)=>a !== 'isNull');
                this.form.data.goodsPropertyId = goodsProperty.id;
                this.form.data.goodsPropertyCategoryId = categoryIds.length? categoryIds.join(','): null;
                this.$set(goodsProperty, 'loading', true);
                this.$refs[goodsProperty.id + '_form'][0].validate((valid) => {
                    if (valid) {
                        util.ajax.post(`/api/goodsPropertyValue`, this.form.data).then(() => {
                            this.$set(goodsProperty, 'modal', false);
                            this.$Message.success('保存成功');
                            this.loadProperties();
                        }).catch((error)=>{
                            this.$set(goodsProperty, 'loading', false);
                            return Promise.reject(error);
                        });
                    } else {
                        this.$set(goodsProperty, 'loading', false);
                    }
                })
            }
        },
        mounted() {
            this.loadProperties();
        },
        watch: {
            selectedGoodsPropertyIds: {
                handler(val, oldVal) {
                    this.selectGoodsProperty();
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