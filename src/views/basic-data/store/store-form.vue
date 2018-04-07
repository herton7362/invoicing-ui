<template>

</template>

<script>
    export default {
        data() {
            return {
                form: {
                    rule: {
                        name: [
                            {required: true, message: '请填写门店名称', trigger: 'blur'}
                        ],
                        code: [
                            {required: true, message: '请填写门店编号', trigger: 'blur'}
                        ],
                        warehouseId: [
                            {required: true, message: '请选择仓库', trigger: 'change'}
                        ],
                        startBusinessTime: [
                            {required: true, message: '请选择开始营业时间', trigger: 'change'}
                        ],
                        closeBusinessTime: [
                            {required: true, message: '请选择关闭营业时间', trigger: 'change'}
                        ],
                        storeManagerId: [
                            {required: true, message: '请选择店长', trigger: 'change'}
                        ],
                        addressId: [
                            {required: true, message: '请选择地址', trigger: 'change'}
                        ],
                        detailAddress: [
                            {required: true, message: '请填写详细地址', trigger: 'blur'}
                        ]
                    },
                    data: {
                        id: null,
                        name: null,
                        code: null,
                        warehouseId: null,
                        startBusinessTime: '08:00:00',
                        closeBusinessTime: '23:00:00',
                        description: null,
                        logicallyDeleted: 0,
                        storeManagerId: null,
                        addressId: null,
                        zipCode: null,
                        detailAddress: null,
                        linkman: null,
                        mobile: null,
                        telephone: null,
                        fax: null,
                        remark: null
                    }
                }
            }
        }
    }
</script>