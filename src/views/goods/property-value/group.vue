<style lang="less">
    @import './group.less';
</style>
<template>
    <div class="goods-property-group">
        <label class="goods-property-group-label" style="width: 80px;">属性类别</label>
        <Tag :ref="`${item.id}_tag`"
             checkable
             type="border"
             :checked="item._checked"
             v-for="item in tag.data"
             :key="item.id"
             @click.native="onCheck(item)">
            {{ item.name }}&nbsp;&nbsp;
            <Poptip placement="bottom" :width="200" transfer v-model="item.modal">
                <Icon type="ios-compose-outline" @click.native.stop="loadOne(item)"></Icon>
                <div slot="content">
                    <Form :ref="`${item.id}_form`" :model="form.data" :rules="form.rule" :label-width="0">
                        <FormItem prop="name">
                            <Input v-model="form.data.name" placeholder="请输入名称" autofocus/>
                        </FormItem>
                        <Button type="primary" long @click="save(item)" :loading="form.loading">保存</Button>
                    </Form>
                </div>
            </Poptip>
            <Poptip title="您确认删除这条内容吗？" confirm transfer @on-ok="remove(item)">
                <Icon type="ios-close-empty"></Icon>
            </Poptip>
        </Tag>
        <Poptip placement="bottom" :width="200" transfer v-model="form.modal">
            <Button icon="ios-plus-empty" type="dashed" size="small" @click="newOne">添加类别</Button>
            <div slot="content">
                <Form ref="form" :model="form.data" :rules="form.rule" :label-width="0">
                    <FormItem prop="name">
                        <Input v-model="form.data.name" placeholder="请输入名称" autofocus/>
                    </FormItem>
                    <Button type="primary" long @click="save()" :loading="form.loading">保存</Button>
                </Form>
            </div>
        </Poptip>
    </div>
</template>

<script>
    import util from '@/libs/util';
    export default {
        props: {
            goodsProperty: Object
        },
        data() {
            return {
                tag: {
                    checked: [],
                    data: []
                },
                form: {
                    loading: false,
                    modal: false,
                    data: {
                        id: null,
                        name: null
                    },
                    rule: {
                        name: [
                            { required: true, message: '请填写名称', trigger: 'blur' }
                        ]
                    }
                }
            }
        },
        methods: {
            save(item) {
                this.form.loading = true;
                this.form.data.goodsPropertyId = this.goodsProperty.id;
                let form;
                if(item) {
                    form = this.$refs[`${item.id}_form`][0];
                } else {
                    form = this.$refs['form'];
                }
                form.validate((valid) => {
                    if (valid) {
                        util.ajax.post(`/api/goodsPropertyCategory`, this.form.data).then(() => {
                            this.$Message.success('保存成功');
                            this.form.modal = false;
                            this.loadGroup();
                        });
                    }
                    this.form.loading = false;
                })
            },
            newOne() {
                this.form.data = {};
            },
            loadOne(item) {
                item.modal = true;
                this.form.data = item;
            },
            remove(item) {
                this.$Modal.confirm({
                    title: '系统提示',
                    content: `您确认删除【${item.name}】吗？`,
                    onOk: () => {
                        util.ajax.delete(`/api/goodsPropertyCategory/${item.id}`).then(() => {
                            this.$Message.success('删除成功');
                            this.loadGroup();
                            this.unCheck(item);
                            this.$emit('on-check', this.tag.checked);
                        });
                    }
                });
            },
            loadGroup() {
                util.ajax.get('/api/goodsPropertyCategory', {
                    params: {
                        goodsPropertyId: this.goodsProperty.id
                    }
                }).then((response)=>{
                    response.data.forEach((d)=>d._checked = false);
                    this.tag.data = response.data;
                    this.$emit('on-load', response.data);
                })
            },
            onCheck(item) {
                if(this.$refs[`${item.id}_tag`][0].isChecked) {
                    if(!this.tag.checked.some((id)=>item.id === id))
                        this.tag.checked.push(item.id);
                } else {
                    this.unCheck(item);
                }
                this.$emit('on-check', this.tag.checked)
            },
            unCheck(item) {
                if(this.tag.checked.some((id)=>item.id === id))
                    this.tag.checked.splice(this.tag.checked.findIndex((id)=>item.id === id), 1);
            }
        }
    }
</script>