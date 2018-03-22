<style lang="less">
    @import '../../../styles/common.less';
    @import './property-group.less';
</style>

<template>
    <Card class="property-group">
        <Button type="dashed" long icon="plus-round" @click="openNewModal"><span>新建</span></Button>
        <div class="property-group-list" v-for="row in list.data">
            <div class="property-group-list-item">
                <div class="property-group-list-item-extra-wrap">
                <div class="property-group-list-item-meta">
                    <div class="property-group-list-item-title">
                        <h4>{{row.name}}</h4>
                        <p class="property-group-list-item-description">{{row.remark}}</p>
                    </div>
                </div>
                <div class="property-group-list-item-meta">
                    <div class="property-group-list-item-content">
                        <p class="property-group-list-item-content-row" v-for="property in row.goodsPropertyResults">
                            <label class="property-group-list-item-content-title">{{property.name}}</label>
                            <Tag color="blue" v-for="value in property.goodsPropertyValueResults">{{value.name}}</Tag>
                        </p>
                    </div>
                </div>
                <div class="property-group-list-item-action">
                    <a href="javascript:void(0)" class="margin-right-small">修改</a>
                    <Poptip confirm transfer title="您确认删除这条内容吗？">
                        <a href="javascript:void(0)" class="margin-right-small">删除</a>
                    </Poptip>
                </div>
                </div>
            </div>
        </div>
        <div></div>
        <edit-modal ref="editModal"></edit-modal>
    </Card>
</template>

<script>
    import util from '@/libs/util';
    import EditModal from './edit-modal.vue';
    export default {
        components: {
            EditModal
        },
        data() {
            return {
                list: {
                    data: []
                }
            }
        },
        methods: {
            openNewModal() {
                this.$refs.editModal.openNewModal();
            },
            loadGrid() {
                util.ajax.get('/api/goodsPropertyGroup').then((response)=>{
                    this.list.data = response.data;
                });
            }
        },
        mounted() {
            this.loadGrid();
        }
    }
</script>