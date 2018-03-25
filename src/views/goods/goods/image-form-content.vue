<style lang="less">
    @import './image-form-content.less';
</style>

<template>
    <div class="goods-images">
        <span>只能上传jpg/png文件，且不超过500kb，建议分辨率为240*240</span>
        <Row class="margin-top-medium">
            <Col :span="8">
            <Upload ref="cover"
                    :show-upload-list="false"
                    :format="['jpg','jpeg','png']"
                    :max-size="1024"
                    name="attachments"
                    :on-success="handleCoverSuccess"
                    :action="'/attachment/upload' | baseUrl">
                <div class="upload-button cover" v-if="!cover.file.id">
                    <Icon class="add-image-btn" type="plus-round"></Icon>
                    <div slot="tip" class="el-upload__tip">封面</div>
                </div>
                <div>
                    <template v-if="cover.file.status === 'finished'">
                        <img width="100%" :src="'/attachment/download/' + cover.file.id | baseUrl">
                        <div class="demo-upload-list-cover">
                            <Icon type="ios-eye-outline"></Icon>
                            <Icon type="ios-trash-outline"></Icon>
                        </div>
                    </template>
                    <template v-else>
                        <Progress v-if="cover.file.showProgress" :percent="cover.file.percentage" hide-info></Progress>
                    </template>
                </div>
            </Upload>
            </Col>
            <Col :span="16" style="padding-left: 55px;">
            <Row>
                <Upload class="upload-wrap"
                        :show-upload-list="false"
                        :format="['jpg','jpeg','png']"
                        :max-size="1024"
                        action="//jsonplaceholder.typicode.com/posts/">
                    <div class="upload-button">
                        <Icon class="add-image-btn" type="plus-round"></Icon>
                        <div slot="tip" class="el-upload__tip">附图1</div>
                    </div>
                </Upload>
                <Upload class="upload-wrap"
                        :show-upload-list="false"
                        :format="['jpg','jpeg','png']"
                        :max-size="1024"
                        action="//jsonplaceholder.typicode.com/posts/">
                    <div class="upload-button">
                        <Icon class="add-image-btn" type="plus-round"></Icon>
                        <div slot="tip" class="el-upload__tip">附图2</div>
                    </div>
                </Upload>
            </Row>
            <Row class="margin-top-large">
                <Upload class="upload-wrap"
                        :show-upload-list="false"
                        :format="['jpg','jpeg','png']"
                        :max-size="1024"
                        action="//jsonplaceholder.typicode.com/posts/">
                    <div class="upload-button">
                        <Icon class="add-image-btn" type="plus-round"></Icon>
                        <div slot="tip" class="el-upload__tip">附图3</div>
                    </div>
                </Upload>
                <Upload class="upload-wrap"
                        :show-upload-list="false"
                        :format="['jpg','jpeg','png']"
                        :max-size="1024"
                        action="//jsonplaceholder.typicode.com/posts/">
                    <div class="upload-button">
                        <Icon class="add-image-btn" type="plus-round"></Icon>
                        <div slot="tip" class="el-upload__tip">附图4</div>
                    </div>
                </Upload>
            </Row>
            </Col>
        </Row>
    </div>
</template>

<script>
    import util from '@/libs/util';
    export default {
        props: {
            formData: Object
        },
        data() {
            return {
                cover: {
                    file: {}
                }
            }
        },
        methods: {
            handleCoverSuccess(res, file) {
                res.forEach((r)=>{
                    file.id = r.id;
                    file.name = r.name;
                });
                this.cover.file = file;
            }
        },
        mounted() {

        },
        filters: {
            baseUrl(val) {
                return util.baseURL + val;
            }
        }
    }
</script>