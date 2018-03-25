<template>
    <Tabs>
        <TabPane label="基本单位">
            <Row>
                <Col :span="8">
                <FormItem label="单位名称" prop="basicGoodsPriceParam.unitName">
                    <Input v-model="formData.basicGoodsPriceParam.unitName" placeholder="单位名称"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="单位关系" prop="basicGoodsPriceParam.conversionRelationship">
                    <InputNumber v-model="formData.basicGoodsPriceParam.conversionRelationship" placeholder="单位关系"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="零售价" prop="basicGoodsPriceParam.retailPrice">
                    <InputNumber v-model="formData.basicGoodsPriceParam.retailPrice" placeholder="零售价"/>
                </FormItem>
                </Col>
            </Row>
            <a href="javascript:void(0)" style="margin-left: 18px;"><Icon type="edit"></Icon> 预设售价</a>
        </TabPane>
        <TabPane label="辅助单位1">
            <Row>
                <Col :span="8">
                <FormItem label="单位名称" prop="assistGoodsPriceParam1.unitName">
                    <Input v-model="formData.assistGoodsPriceParam1.unitName" placeholder="单位名称"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="单位关系" prop="assistGoodsPriceParam1.conversionRelationship">
                    <InputNumber v-model="formData.assistGoodsPriceParam1.conversionRelationship" placeholder="单位关系"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="零售价" prop="assistGoodsPriceParam1.retailPrice">
                    <InputNumber v-model="formData.assistGoodsPriceParam1.retailPrice" placeholder="零售价"/>
                </FormItem>
                </Col>
            </Row>
            <a href="javascript:void(0)" style="margin-left: 18px;"><Icon type="edit"></Icon> 预设售价</a>
        </TabPane>
        <TabPane label="辅助单位2">
            <Row>
                <Col :span="8">
                <FormItem label="单位名称">
                    <Input v-model="formData.costPrice" placeholder="单位名称"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="单位关系">
                    <InputNumber v-model="formData.costPrice" placeholder="单位关系"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="零售价">
                    <InputNumber v-model="formData.costPrice" placeholder="零售价"/>
                </FormItem>
                </Col>
            </Row>
            <a href="javascript:void(0)" style="margin-left: 18px;"><Icon type="edit"></Icon> 预设售价</a>
        </TabPane>
    </Tabs>
</template>

<script>
    export default {
        props: {
            formData: Object
        }
    }
</script>