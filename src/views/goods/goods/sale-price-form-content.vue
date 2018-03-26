<template>
    <Tabs>
        <TabPane label="基本单位">
            <Row>
                <Col :span="8">
                <FormItem label="单位名称" prop="basicGoodsPrice.unitName">
                    <Input v-model="formData.basicGoodsPrice.unitName" placeholder="单位名称"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="单位关系" prop="basicGoodsPrice.conversionRelationship">
                    <InputNumber v-model="formData.basicGoodsPrice.conversionRelationship" placeholder="单位关系"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="零售价" prop="basicGoodsPrice.retailPrice">
                    <InputNumber v-model="formData.basicGoodsPrice.retailPrice" placeholder="零售价"/>
                </FormItem>
                </Col>
            </Row>
            <Row>
                <Col :span="8">
                <FormItem label="预设售价1" prop="basicGoodsPrice.presetPrice1">
                    <InputNumber v-model="formData.basicGoodsPrice.presetPrice1" placeholder="预设售价1"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="预设售价2" prop="basicGoodsPrice.presetPrice2">
                    <InputNumber v-model="formData.basicGoodsPrice.presetPrice2" placeholder="预设售价2"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="预设售价3" prop="basicGoodsPrice.presetPrice3">
                    <InputNumber v-model="formData.basicGoodsPrice.presetPrice3" placeholder="预设售价3"/>
                </FormItem>
                </Col>
            </Row>
            <Row>
                <Col :span="8">
                <FormItem label="预设售价4" prop="basicGoodsPrice.presetPrice4">
                    <InputNumber v-model="formData.basicGoodsPrice.presetPrice4" placeholder="预设售价4"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="预设售价5" prop="basicGoodsPrice.presetPrice5">
                    <InputNumber v-model="formData.basicGoodsPrice.presetPrice5" placeholder="预设售价5"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="预设售价6" prop="basicGoodsPrice.presetPrice6">
                    <InputNumber v-model="formData.basicGoodsPrice.presetPrice6" placeholder="预设售价6"/>
                </FormItem>
                </Col>
            </Row>
            <Row>
                <Col :span="8">
                <FormItem label="预设售价7" prop="basicGoodsPrice.presetPrice7">
                    <InputNumber v-model="formData.basicGoodsPrice.presetPrice7" placeholder="预设售价7"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="预设售价8" prop="basicGoodsPrice.presetPrice8">
                    <InputNumber v-model="formData.basicGoodsPrice.presetPrice8" placeholder="预设售价8"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="预设售价9" prop="basicGoodsPrice.presetPrice9">
                    <InputNumber v-model="formData.basicGoodsPrice.presetPrice9" placeholder="预设售价9"/>
                </FormItem>
                </Col>
            </Row>
            <Row>
                <Col :span="8">
                <FormItem label="预设售价10" prop="basicGoodsPrice.presetPrice10">
                    <InputNumber v-model="formData.basicGoodsPrice.presetPrice10" placeholder="预设售价10"/>
                </FormItem>
                </Col>
            </Row>
        </TabPane>
        <TabPane label="辅助单位1">
            <Row>
                <Col :span="8">
                <FormItem label="单位名称" prop="assistGoodsPrice1.unitName">
                    <Input v-model="formData.assistGoodsPrice1.unitName" placeholder="单位名称"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="单位关系" prop="assistGoodsPrice1.conversionRelationship">
                    <InputNumber v-model="formData.assistGoodsPrice1.conversionRelationship" placeholder="单位关系"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="零售价" prop="assistGoodsPrice1.retailPrice">
                    <InputNumber v-model="formData.assistGoodsPrice1.retailPrice" placeholder="零售价"/>
                </FormItem>
                </Col>
            </Row>
            <Row>
                <Col :span="8">
                <FormItem label="预设售价1" prop="assistGoodsPrice1.presetPrice1">
                    <InputNumber v-model="formData.assistGoodsPrice1.presetPrice1" placeholder="预设售价1"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="预设售价2" prop="assistGoodsPrice1.presetPrice2">
                    <InputNumber v-model="formData.assistGoodsPrice1.presetPrice2" placeholder="预设售价2"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="预设售价3" prop="assistGoodsPrice1.presetPrice3">
                    <InputNumber v-model="formData.assistGoodsPrice1.presetPrice3" placeholder="预设售价3"/>
                </FormItem>
                </Col>
            </Row>
            <Row>
                <Col :span="8">
                <FormItem label="预设售价4" prop="assistGoodsPrice1.presetPrice4">
                    <InputNumber v-model="formData.assistGoodsPrice1.presetPrice4" placeholder="预设售价4"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="预设售价5" prop="assistGoodsPrice1.presetPrice5">
                    <InputNumber v-model="formData.assistGoodsPrice1.presetPrice5" placeholder="预设售价5"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="预设售价6" prop="assistGoodsPrice1.presetPrice6">
                    <InputNumber v-model="formData.assistGoodsPrice1.presetPrice6" placeholder="预设售价6"/>
                </FormItem>
                </Col>
            </Row>
            <Row>
                <Col :span="8">
                <FormItem label="预设售价7" prop="assistGoodsPrice1.presetPrice7">
                    <InputNumber v-model="formData.assistGoodsPrice1.presetPrice7" placeholder="预设售价7"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="预设售价8" prop="assistGoodsPrice1.presetPrice8">
                    <InputNumber v-model="formData.assistGoodsPrice1.presetPrice8" placeholder="预设售价8"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="预设售价9" prop="assistGoodsPrice1.presetPrice9">
                    <InputNumber v-model="formData.assistGoodsPrice1.presetPrice9" placeholder="预设售价9"/>
                </FormItem>
                </Col>
            </Row>
            <Row>
                <Col :span="8">
                <FormItem label="预设售价10" prop="assistGoodsPrice1.presetPrice10">
                    <InputNumber v-model="formData.assistGoodsPrice1.presetPrice10" placeholder="预设售价10"/>
                </FormItem>
                </Col>
            </Row>
        </TabPane>
        <TabPane label="辅助单位2">
            <Row>
                <Col :span="8">
                <FormItem label="单位名称" prop="assistGoodsPrice2.unitName">
                    <Input v-model="formData.assistGoodsPrice2.unitName" placeholder="单位名称"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="单位关系" prop="assistGoodsPrice2.conversionRelationship">
                    <InputNumber v-model="formData.assistGoodsPrice2.conversionRelationship" placeholder="单位关系"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="零售价" prop="assistGoodsPrice2.retailPrice">
                    <InputNumber v-model="formData.assistGoodsPrice2.retailPrice" placeholder="零售价"/>
                </FormItem>
                </Col>
            </Row>
            <Row>
                <Col :span="8">
                <FormItem label="预设售价1" prop="assistGoodsPrice2.presetPrice1">
                    <InputNumber v-model="formData.assistGoodsPrice2.presetPrice1" placeholder="预设售价1"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="预设售价2" prop="assistGoodsPrice2.presetPrice2">
                    <InputNumber v-model="formData.assistGoodsPrice2.presetPrice2" placeholder="预设售价2"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="预设售价3" prop="assistGoodsPrice2.presetPrice3">
                    <InputNumber v-model="formData.assistGoodsPrice2.presetPrice3" placeholder="预设售价3"/>
                </FormItem>
                </Col>
            </Row>
            <Row>
                <Col :span="8">
                <FormItem label="预设售价4" prop="assistGoodsPrice2.presetPrice4">
                    <InputNumber v-model="formData.assistGoodsPrice2.presetPrice4" placeholder="预设售价4"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="预设售价5" prop="assistGoodsPrice2.presetPrice5">
                    <InputNumber v-model="formData.assistGoodsPrice2.presetPrice5" placeholder="预设售价5"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="预设售价6" prop="assistGoodsPrice2.presetPrice6">
                    <InputNumber v-model="formData.assistGoodsPrice2.presetPrice6" placeholder="预设售价6"/>
                </FormItem>
                </Col>
            </Row>
            <Row>
                <Col :span="8">
                <FormItem label="预设售价7" prop="assistGoodsPrice2.presetPrice7">
                    <InputNumber v-model="formData.assistGoodsPrice2.presetPrice7" placeholder="预设售价7"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="预设售价8" prop="assistGoodsPrice2.presetPrice8">
                    <InputNumber v-model="formData.assistGoodsPrice2.presetPrice8" placeholder="预设售价8"/>
                </FormItem>
                </Col>
                <Col :span="8">
                <FormItem label="预设售价9" prop="assistGoodsPrice2.presetPrice9">
                    <InputNumber v-model="formData.assistGoodsPrice2.presetPrice9" placeholder="预设售价9"/>
                </FormItem>
                </Col>
            </Row>
            <Row>
                <Col :span="8">
                <FormItem label="预设售价10" prop="assistGoodsPrice2.presetPrice10">
                    <InputNumber v-model="formData.assistGoodsPrice2.presetPrice10" placeholder="预设售价10"/>
                </FormItem>
                </Col>
            </Row>
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