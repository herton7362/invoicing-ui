<style lang="less">
    @import '../../../styles/common.less';
</style>

<template>
    <Card>
        <single-table ref="table"
                      :columns="table.columns"
                      form-title="门店维护"
                      domain-url="store"
                      :form-rule="form.rule"
                      :form-data="form.data">
            <template slot="query-form" slot-scope="props">
            </template>

            <template slot="edit-form" slot-scope="props">
            </template>
        </single-table>
    </Card>
</template>

<script>
    import util from '@/libs/util';
    import SingleTable from '@/views/my-components/single-table/single-table.vue';

    export default {
        components: {
            SingleTable
        },
        data() {
            return {
                table: {
                    columns: [
                    ]
                },
                form: {
                    rule: {
                    },
                    data: {
                        id: null
                    }
                }
            }
        },
        methods: {
        },
        mounted() {
            this.$refs.table.loadGrid();
        }
    };
</script>