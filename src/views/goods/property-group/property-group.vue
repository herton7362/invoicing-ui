<style lang="less">
    @import '../../../styles/common.less';
    @import './property-group.less';
</style>

<template>
    <Card class="property-group">
        <Button type="dashed" long icon="plus-round"><span>新建</span></Button>
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
                        <p>颜色: 红色,黑色,白色,蓝色,黄色</p>
                        <p>尺码: XS,S,M,L,XL,XXL,XXXL,均码</p>
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
    </Card>
</template>

<script>
    import util from '@/libs/util';
    export default {
        data() {
            return {
                form: {
                    data: {
                        id: null,
                        remark: null
                    }
                }
            }
        }
    }
</script>