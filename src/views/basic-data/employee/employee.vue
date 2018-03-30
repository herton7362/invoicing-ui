<style lang="less">
    @import '../../../styles/common.less';
    @import './employee.less';
</style>

<template>
    <div class="employee">
        <div class="employee-head">
            <Alert show-icon>根据员工的职能选择角色，然后新增账号。可以自定义角色，进行权限配置。</Alert>
            <Button type="primary">员工列表</Button>
        </div>

        <Row :gutter="24">
            <Col :sm="12" :md="8">
            <Button class="employee-add" long type="dashed" icon="ios-plus-empty">
                新增角色
            </Button>
            </Col>
            <Col :sm="12" :md="8">
            <Card class="employee-block">
                <Avatar icon="locked" size="large" />
                <div class="employee-block-detail">
                    <div class="employee-block-title">
                        管理员
                        <Button class="config" type="text" size="small">
                            <span class="text">配置权限</span> <Icon type="gear-a"></Icon>
                        </Button>
                    </div>
                    <div class="employee-block-description">
                        <div>
                            管理员具有平台全部功能使用权限。请谨慎配置。
                        </div>
                    </div>
                </div>
                <ul class="employee-actions">
                    <li style="width: 50%;">
                        <Button type="text" long>新增账号</Button>
                    </li>
                    <li style="width: 50%;">
                        <Button type="text" long>管理账号</Button>
                    </li>
                </ul>
            </Card>
            </Col>
            <Col :sm="12" :md="8">
            <Card class="employee-block">
                <Avatar icon="social-yen" size="large" />
                <div class="employee-block-detail">
                    <div class="employee-block-title">
                        收银
                        <Button class="config" type="text" size="small">
                            <span class="text">配置权限</span> <Icon type="gear-a"></Icon>
                        </Button>
                    </div>
                    <div class="employee-block-description">
                        <div>
                            收银能够进行零售、退款等日常经营中遇到的问题。
                        </div>
                    </div>
                </div>
                <ul class="employee-actions">
                    <li style="width: 50%;">
                        <Button type="text" long>新增账号</Button>
                    </li>
                    <li style="width: 50%;">
                        <Button type="text" long>管理账号</Button>
                    </li>
                </ul>
            </Card>
            </Col>
        </Row>
    </div>

</template>

<script>
    import util from '@/libs/util';
    export default {

    }
</script>