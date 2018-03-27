<style lang="less">
</style>
<template>
    <div>
        <Row>
            <Col :span="8">
            <FormItem label="属性组" prop="goodsPropertyGroupId" :label-width="70">
                <Select v-model="formData.goodsPropertyGroupId" clearable placeholder="请选择属性组">
                    <Option v-for="group in groups" :value="group.id" :key="group.id">{{group.name}}</Option>
                    <Spin v-if="loading" style="margin: 0 auto;"></Spin>
                </Select>
            </FormItem>
            </Col>
        </Row>
        <property-selector :default-selected-data="defaultSelectedData"
                           @on-select-change="onSelectChange"></property-selector>
    </div>
</template>

<script>
    import util from '@/libs/util';
    import PropertySelector from '../property-group/property-selector.vue';
    export default {
        components: {
            PropertySelector
        },
        props: {
            formData: Object
        },
        data() {
            return {
                loading: false,
                groups: [],
                groupLoader: null,
                defaultSelectedData: {}
            }
        },
        methods: {
            loadGroup() {
                this.loading = true;
                this.groupLoader = util.ajax.get('/api/goodsPropertyGroup');
                this.groupLoader.then((response)=>{
                    this.groups = response.data;
                    this.loading = false;
                })
            },
            onSelectChange(data) {
                const goodsPropertyGroupProperties = [];
                for(let key in data) {
                    if(!data[key]) {
                        continue;
                    }
                    const goodsPropertyGroupPropertyValues = [];
                    data[key].forEach((d)=>{
                        goodsPropertyGroupPropertyValues.push({
                            goodsPropertyValueId: d
                        });
                    })
                    goodsPropertyGroupProperties.push({
                        goodsPropertyId: key,
                        goodsGoodsPropertyValues: goodsPropertyGroupPropertyValues
                    });
                }
                this.formData.goodsGoodsProperties = goodsPropertyGroupProperties;
            }
        },
        mounted() {
            this.loadGroup();
        },
        watch: {
            'formData.goodsPropertyGroupId': {
                handler(val, oldVal) {
                    if(!val) {
                        this.defaultSelectedData = {};
                    } else {
                        this.groupLoader.then((response)=>{
                            this.defaultSelectedData = {};
                            this.groups.find((g)=>g.id === val).goodsPropertyResults.forEach((g)=>{
                                const goodsPropertyGroupProperties = [];
                                g.goodsPropertyValueResults.forEach((v)=>{
                                    goodsPropertyGroupProperties.push(v.id);
                                })
                                this.defaultSelectedData[g.id] = goodsPropertyGroupProperties;
                            });
                        });
                    }
                },
                deep: true
            }
        }
    }
</script>