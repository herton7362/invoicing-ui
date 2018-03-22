<style lang="less">
    @import '../../../styles/common.less';
    @import './property-group.less';
</style>

<template>
    <Card class="property-group">
        <Button type="dashed" long icon="plus-round" @click="openNewModal"><span>新建</span></Button>
        <div class="property-group-list">
            <div class="property-group-list-item">
                <div class="property-group-list-item-extra-wrap">
                <div class="property-group-list-item-meta">
                    <div class="property-group-list-item-title">
                        <h4>女装属性组</h4>
                        <p class="property-group-list-item-description">备注</p>
                    </div>
                </div>
                <div class="property-group-list-item-meta">
                    <div class="property-group-list-item-content">
                        <p class="property-group-list-item-content-row">
                            <label class="property-group-list-item-content-title">颜色</label>
                            <Tag color="blue">红色</Tag>
                            <Tag color="blue">黑色</Tag>
                            <Tag color="blue">白色</Tag>
                            <Tag color="blue">蓝色</Tag>
                            <Tag color="blue">黄色</Tag>
                        </p>
                        <p class="property-group-list-item-content-row">
                            <label class="property-group-list-item-content-title">尺码</label>
                            <Tag color="blue">XS</Tag>
                            <Tag color="blue">S</Tag>
                            <Tag color="blue">M</Tag>
                            <Tag color="blue">L</Tag>
                            <Tag color="blue">XL</Tag>
                            <Tag color="blue">XXL</Tag>
                            <Tag color="blue">XXXL</Tag>
                            <Tag color="blue">均码</Tag>
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
        <div class="property-group-list">
            <div class="property-group-list-item">
                <div class="property-group-list-item-extra-wrap">
                    <div class="property-group-list-item-meta">
                        <div class="property-group-list-item-title">
                            <h4>女鞋属性组</h4>
                            <p class="property-group-list-item-description">备注</p>
                        </div>
                    </div>
                    <div class="property-group-list-item-meta">
                        <div class="property-group-list-item-content">
                            <p class="property-group-list-item-content-row">
                                <label class="property-group-list-item-content-title">颜色</label>
                                <Tag color="blue">红色</Tag>
                                <Tag color="blue">黑色</Tag>
                                <Tag color="blue">白色</Tag>
                                <Tag color="blue">蓝色</Tag>
                                <Tag color="blue">黄色</Tag>
                            </p>
                            <p class="property-group-list-item-content-row">
                                <label class="property-group-list-item-content-title">尺码</label>
                                <Tag color="blue">34</Tag>
                                <Tag color="blue">35</Tag>
                                <Tag color="blue">36</Tag>
                                <Tag color="blue">37</Tag>
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
            }
        },
        methods: {
            openNewModal() {
                this.$refs.editModal.openNewModal();
            }
        }
    }
</script>