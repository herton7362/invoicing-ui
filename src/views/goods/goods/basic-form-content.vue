<template>
    <div>
        <Row>
            <Col :span="12">
            <FormItem label="所属分类" prop="goodsCategoryId">
                <Select v-model="formData.goodsCategoryId" placeholder="商品分类" clearable>
                    <Option :key="data.id" :value="data.id" v-for="data in categoryData">{{data.name}}</Option>
                </Select>
            </FormItem>
            </Col>
            <Col :span="12">
            <FormItem label="商品名称" prop="name">
                <Input v-model="formData.name" placeholder="商品名称" @on-change="onNameChange"/>
            </FormItem>
            </Col>
        </Row>
        <Row>
            <Col :span="8">
            <FormItem label="商品编码" prop="code">
                <Input v-model="formData.code" placeholder="商品编码"/>
            </FormItem>
            </Col>
            <Col :span="8">
            <FormItem label="商品简名" prop="shortname">
                <Input v-model="formData.shortname" placeholder="商品简名"/>
            </FormItem>
            </Col>
            <Col :span="8">
            <FormItem label="拼音码" prop="pinyin">
                <Input v-model="formData.pinyin" placeholder="拼音码"/>
            </FormItem>
            </Col>
        </Row>
        <Row>
            <Col :span="8">
            <FormItem label="品牌" prop="brandId">
                <Input v-model="formData.brandId" placeholder="品牌"/>
            </FormItem>
            </Col>
            <Col :span="8">
            <FormItem label="产地" prop="madeAddress">
                <Input v-model="formData.madeAddress" placeholder="产地"/>
            </FormItem>
            </Col>
            <Col :span="8">
            <FormItem label="规格" prop="standard">
                <Input v-model="formData.standard" placeholder="规格"/>
            </FormItem>
            </Col>
        </Row>
        <Row>
            <Col :span="8">
            <FormItem label="型号" prop="model">
                <Input v-model="formData.model" placeholder="型号"/>
            </FormItem>
            </Col>
            <Col :span="8">
            <FormItem label="重量（kg）" prop="weight">
                <InputNumber v-model="formData.weight" placeholder="重量（kg）"/>
            </FormItem>
            </Col>
            <Col :span="8">
            <FormItem label="长度" prop="length">
                <InputNumber v-model="formData.length" placeholder="长度"/>
            </FormItem>
            </Col>
        </Row>
        <Row>
            <Col :span="8">
            <FormItem label="宽度" prop="width">
                <InputNumber v-model="formData.width" placeholder="宽度"/>
            </FormItem>
            </Col>
            <Col :span="8">
            <FormItem label="高度" prop="height">
                <InputNumber v-model="formData.height" placeholder="高度"/>
            </FormItem>
            </Col>
        </Row>
        <Row>
            <Col :span="12">
            <FormItem label="备注" prop="remark">
                <Input v-model="formData.remark" placeholder="备注"/>
            </FormItem>
            </Col>
            <Col :span="12">
            <FormItem label="参考成本价（基本单位）" prop="costPrice" :label-width="160">
                <InputNumber v-model="formData.costPrice" placeholder="参考成本价（基本单位）"/>
            </FormItem>
            </Col>
        </Row>
    </div>
</template>

<script>
    import util from '@/libs/util';
    export default {
        props: {
            formData: Object,
            tree: Object
        },
        methods: {
            onNameChange() {
                let name = this.formData.name;
                if(name) {
                    this.formData.shortname = name;
                    this.formData.pinyin = util.getFirstPinyinLetter(name);
                }
            }
        },
        computed: {
            categoryData() {
                let data = [...this.tree.tree.raw];
                data.splice(data.length - 1, 1);
                return data;
            }
        }
    }
</script>