<style lang="less">
    @import '.././property-group/property-group.less';
    .property-group-list-item {
        margin-left: 20px;
        border-bottom: none;
    }
    .property-group-list-item-content {
        max-width: 620px;
    }

</style>
<template>
    <div>
        <Row>
            <Col :span="8">
            <FormItem label="属性组" prop="goodsPropertyGroupId" :label-width="70">
                <Select v-model="formData.goodsPropertyGroupId" placeholder="请选择属性组">
                    <Option v-for="group in groups" :value="group.id" :key="group.id">{{group.name}}</Option>
                </Select>
            </FormItem>
            </Col>
        </Row>
        <div class="property-group-list-item">
            <div class="property-group-list-item-extra-wrap">
                <div class="property-group-list-item-meta">
                    <div class="property-group-list-item-content">
                        <Row :key="property.id" class="property-group-list-item-content-row" v-for="property in selectedGroup.goodsPropertyResults">
                            <Col :span="2">
                            <label class="property-group-list-item-content-title">{{property.name}}</label>
                            </Col>
                            <Col :span="22">
                            <Tag :key="value.id" color="blue" v-for="value in property.goodsPropertyValueResults">{{value.name}}</Tag>
                            </Col>
                        </Row>
                    </div>
                </div>
            </div>
        </div>
        <Spin size="large" fix v-if="loading"></Spin>
    </div>
</template>

<script>
    import util from '@/libs/util';
    export default {
        props: {
            formData: Object
        },
        data() {
            return {
                loading: false,
                groups: [],
                selectedGroup: {}
            }
        },
        methods: {
            loadGroup() {
                this.loading = true;
                util.ajax.get('/api/goodsPropertyGroup').then((response)=>{
                    this.groups = response.data;
                    this.loading = false;
                });
            }
        },
        mounted() {
            this.loadGroup();
        },
        watch: {
            'formData.goodsPropertyGroupId': {
                handler(val) {
                    this.selectedGroup = this.groups.find((g)=>g.id === val);
                }
            }
        }
    }
</script>