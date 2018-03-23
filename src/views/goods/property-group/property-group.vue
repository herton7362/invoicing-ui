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
                                <Tag :key="value.id" color="blue" v-for="value in property.goodsPropertyValueResults">{{value.name}}</Tag>
                            </p>
                        </div>
                    </div>
                    <div class="property-group-list-item-action">
                        <a href="javascript:void(0)" class="margin-right-small" @click="openEditModal(row)">修改</a>
                        <Poptip confirm transfer title="您确认删除这条内容吗？" @on-ok="remove(row)">
                            <a href="javascript:void(0)" class="margin-right-small">删除</a>
                        </Poptip>
                    </div>
                </div>
            </div>
        </div>
        <div></div>
        <edit-modal ref="editModal" @on-save-success="loadGrid"></edit-modal>
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
            openEditModal(row) {
                this.$refs.editModal.openEditModal(row);
            },
            loadGrid() {
                util.ajax.get('/api/goodsPropertyGroup').then((response)=>{
                    this.list.data = response.data;
                });
            },
            remove(row) {
                util.ajax.delete(`/api/goodsPropertyGroup/${row.id}`).then(()=>{
                    this.$Message.success('删除成功');
                    this.loadGrid();
                })
            }
        },
        mounted() {
            this.loadGrid();
        }
    }
</script>