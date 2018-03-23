<style lang="less">
    @import './edit-modal.less';
</style>

<template>
    <Modal class="property-group-edit"
           v-model="form.modal"
           title="属性组维护"
           :loading="form.loading"
           @on-ok="save">
        <Form ref="form" :model="form.data" :rules="form.rule" :label-width="80">
            <Row>
                <Col :span="12">
                <FormItem label="名称" prop="name">
                    <Input v-model="form.data.name" placeholder="请输入名称"/>
                </FormItem>
                </Col>
                <Col :span="12">
                <FormItem label="备注" prop="remark">
                    <Input v-model="form.data.remark" placeholder="请输入备注"/>
                </FormItem>
                </Col>
            </Row>
            <FormItem label="属性" prop="goodsPropertyIds">
                <CheckboxGroup v-model="form.data.goodsPropertyIds">
                    <Checkbox :key="goodsProperty.id"
                              :label="goodsProperty.id"
                              v-for="goodsProperty in goodsProperties">{{goodsProperty.name}}</Checkbox>
                </CheckboxGroup>
            </FormItem>
            <FormItem :class="{'property-group-edit-category-expanded': !hideMoreCategory}"
                      :key="id"
                      :label="getGroupPropertyName(id) + '属性'"
                      v-for="id in form.data.goodsPropertyIds">
                <Tag :ref="id + 'isNull_tag'"
                     size="small"
                     checkable
                     :checked="ifTagChecked(id, 'isNull')"
                     type="border"
                     @click.native="checkTag(id, 'isNull')">未分组</Tag>
                <Tag :ref="id + propertyCategory.id + '_tag'"
                     size="small"
                     :key="propertyCategory.id"
                     checkable
                     :checked="ifTagChecked(id, propertyCategory.id)"
                     @click.native="checkTag(id, propertyCategory.id)"
                     type="border"
                     v-show="index < 3 || !hideMoreCategory"
                     v-for="(propertyCategory, index) in goodsPropertyCategories[id]">{{propertyCategory.name}}</Tag>
                <a href="javascript:void(0)"
                   class="expand-handler"
                   v-if="goodsPropertyCategories[id] && goodsPropertyCategories[id].length > 3"
                   @click="hideMoreCategory = !hideMoreCategory">
                    {{hideMoreCategory? '展开': '收起'}} <Icon type="chevron-down"></Icon>
                </a>
                <CheckboxGroup v-model="form.data.goodsPropertyValueIds">
                    <Checkbox :key="propertyValue.id"
                              :label="propertyValue.id"
                              v-for="propertyValue in propertyValues[id]">{{propertyValue.name}}</Checkbox>
                </CheckboxGroup>
                <a href="javascript:void(0)"><Icon type="ios-plus-empty"></Icon> 添加属性值</a>
            </FormItem>
        </Form>
        <Spin size="large" fix v-if="form.data.loading"></Spin>
    </Modal>
</template>

<script>
    import util from '@/libs/util';
    import axios from 'axios';

    export default {
        data() {
            return {
                form: {
                    modal: false,
                    loading: true,
                    data: {
                        id: null,
                        name: null,
                        remark: null,
                        goodsPropertyIds: [],
                        goodsPropertyValueIds: [],
                        loading: false
                    },
                    rule: {
                        name: [
                            { required: true, message: '请填写属性组名称', trigger: 'blur' }
                        ]
                    }
                },
                goodsProperties: [],
                propertyValues: {},
                goodsPropertyCategories: {},
                propertyValueQueryParam: {},
                hideMoreCategory: true
            }
        },
        methods: {
            openNewModal() {
                this.$refs.form.resetFields();
                this.form.data.id = null;// 解决清空表单id不会删除问题
                this.propertyValues = {};
                this.goodsPropertyCategories = {};
                this.propertyValueQueryParam = {};
                this.form.modal = true;
            },
            openEditModal(row) {
                this.$refs.form.resetFields();
                this.propertyValues = {};
                this.goodsPropertyCategories = {};
                this.propertyValueQueryParam = {};
                util.ajax.get(`/api/goodsPropertyGroup/${row.id}`).then((response)=>{
                    this.form.data = response.data;
                    this.form.data.goodsPropertyResults.forEach((result)=>{
                        result.goodsPropertyValueResults.forEach((sub)=>{
                            if(this.propertyValueQueryParam[sub.goodsPropertyId] === undefined) {
                                this.$set(this.propertyValueQueryParam, sub.goodsPropertyId, {goodsPropertyCategoryId: []});
                            }
                            if(sub.goodsPropertyCategoryId) {
                                this.addPropertyValueQueryParam(sub.goodsPropertyId, sub.goodsPropertyCategoryId);
                            }
                        });
                        this.loadPropertyValues(result.id);
                    });
                    this.form.modal = true;
                });
            },
            loadProperties() {
                util.ajax.get('/api/goodsProperty').then((response)=>{
                    this.goodsProperties = response.data;
                });
            },
            loadPropertyValues(propertyId) {
                util.ajax.get('/api/goodsPropertyValue', {
                    params: {
                        goodsPropertyId: propertyId,
                        ...this.propertyValueQueryParam[propertyId]
                    }
                }).then((response)=>{
                    this.$set(this.propertyValues, propertyId, response.data);
                });
            },
            loadPropertyCategories(propertyId) {
                let ajax = util.ajax.get('/api/goodsPropertyCategory', {
                    params: {
                        goodsPropertyId: propertyId
                    }
                })
                ajax.then((response)=>{
                    this.$set(this.goodsPropertyCategories, propertyId, response.data);
                });
                return ajax;
            },
            getGroupPropertyName(id) {
                return this.goodsProperties.find((d)=>d.id === id).name;
            },
            ifTagChecked(id, goodsPropertyCategoryId) {
                if(this.propertyValueQueryParam[id]) {
                    return this.propertyValueQueryParam[id].goodsPropertyCategoryId.includes(goodsPropertyCategoryId);
                }
                return false;
            },
            checkTag(id, goodsPropertyCategoryId) {
                if(this.$refs[id + goodsPropertyCategoryId + '_tag'][0].isChecked) {
                    this.addPropertyValueQueryParam(id, goodsPropertyCategoryId);
                } else {
                    this.removePropertyValueQueryParam(id, goodsPropertyCategoryId);
                }
                this.loadPropertyValues(id);
            },
            addPropertyValueQueryParam(id, goodsPropertyCategoryId) {
                const array = this.propertyValueQueryParam[id].goodsPropertyCategoryId;
                if(!array.includes(goodsPropertyCategoryId)) {
                    array.push(goodsPropertyCategoryId)
                }
            },
            removePropertyValueQueryParam(id, goodsPropertyCategoryId) {
                const array = this.propertyValueQueryParam[id].goodsPropertyCategoryId;
                if(array.includes(goodsPropertyCategoryId)) {
                    array.splice(array.findIndex((a)=>a === goodsPropertyCategoryId), 1);
                }
            },
            save() {
                this.$refs.form.validate((valid) => {
                    if (valid) {
                        util.ajax.post(`/api/goodsPropertyGroup`, this.form.data).then(() => {
                            this.form.modal = false;
                            this.$Message.success('保存成功');
                            this.$emit('on-save-success');
                        }).catch((error)=>{
                            this.clearFormLoading();
                            return Promise.reject(error);
                        });
                    } else {
                        this.clearFormLoading();
                    }
                })
            },
            clearFormLoading() {
                this.form.loading = false;
                this.$nextTick(() => {
                    this.form.loading = true;
                });
            }
        },
        mounted() {
            this.loadProperties();
        },
        watch: {
            'form.data.goodsPropertyIds': {
                handler(val) {
                    val.forEach((v)=>{
                        if(this.goodsPropertyCategories[v] === undefined) {
                            this.form.data.loading = true;
                            if(this.propertyValueQueryParam[v] === undefined)
                                this.$set(this.propertyValueQueryParam, v, {goodsPropertyCategoryId: []});
                            this.loadPropertyCategories(v).then((response)=>{
                                // 如果没有分组则直接加载属性值
                                if(response.data.length === 0) {
                                    this.$refs[v + 'isNull_tag'][0].isChecked = true;
                                    this.$nextTick(()=>this.checkTag(v, 'isNull'));
                                }
                                this.form.data.loading = false;
                            });
                        }
                    })
                },
                deep: true
            }
        }
    }
</script>