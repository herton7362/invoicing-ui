<style lang="less">
    @import '../../../styles/common.less';
</style>

<template>
    <Card>
        <paged-table ref="table" :columns="table.member.columns">
            <template slot="query-form" slot-scope="props">
                <FormItem class="padding-right-medium" prop="name" label="会员名称">
                    <Input v-model="props.params.name" placeholder="会员名称"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="mobile" label="电话">
                    <Input v-model="props.params.mobile" placeholder="电话"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="code" label="会员编号">
                    <Input v-model="props.params.code" placeholder="会员编号"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="status" label="会员状态">
                    <RadioGroup v-model="props.params.status">
                        <Radio label="ENABLE">启用</Radio>
                        <Radio label="DISABLE">停用</Radio>
                        <Radio label="">所有</Radio>
                    </RadioGroup>
                </FormItem>
                <FormItem class="padding-right-medium" prop="cardNo" label="会员卡号">
                    <Input v-model="props.params.cardNo" placeholder="会员卡号"/>
                </FormItem>
                <FormItem class="padding-right-medium" prop="status" label="会员卡类型">
                    <Select v-model="props.params.status">
                        <Option value="">全部</Option>
                    </Select>
                </FormItem>
            </template>
            <template slot="actions">
                <Button type="primary"> <Icon type="plus"></Icon><span>会员卡类型管理</span></Button>
            </template>
        </paged-table>
        <Table class="margin-top-large" :columns="table.card.columns"></Table>
    </Card>
</template>

<script>
    import util from '@/libs/util';
    import PagedTable from '@/views/my-components/single-table/paged-table.vue';

    export default {
        components: {
            PagedTable
        },
        data() {
            return {
                table: {
                    member: {
                        columns: [
                            {key:'code', title:'会员编号'},
                            {key:'name', title:'会员名'},
                            {key:'mobile', title:'电话'},
                            {key:'licenseNumber', title:'证件号码'},
                            {key:'balance', title:'账户储值余额'},
                            {key:'points', title:'积分'},
                            {key:'status', title:'状态'},
                            {key:'inputManner', title:'录入方式'},
                            {key:'remark', title:'备注'}
                        ]
                    },
                    card: {
                        columns: [
                            {key:'code', title:'会员卡号'},
                            {key:'code', title:'会员卡类型'},
                            {key:'code', title:'储值余额'},
                            {key:'code', title:'积分'},
                            {key:'code', title:'折扣'},
                            {key:'code', title:'发卡日期'},
                            {key:'code', title:'到期日期'},
                            {key:'code', title:'状态'},
                            {key:'code', title:'备注'}
                        ]
                    }
                }
            }
        },
        methods: {
        },
        mounted() {
        }
    };
</script>