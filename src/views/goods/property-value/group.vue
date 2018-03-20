<template>
    <Poptip placement="right" :width="200" transfer v-model="form.modal">
        <Tag :ref="item.id"
             checkable
             :checked="item._checked"
             v-for="item in tag.data"
             :key="item.id"
             closable
             @click.native="onCheck(item)"
             @on-close="remove(item)">
            {{ item.name }}
        </Tag>
        <Button icon="ios-plus-empty" type="dashed" size="small">添加组</Button>
        <div slot="content">
            <Form ref="form" :model="form.data" :rules="form.rule" :label-width="0">
                <FormItem prop="name">
                    <Input v-model="form.data.name" placeholder="请输入名称"/>
                </FormItem>
                <Button type="primary" long @click="save" :loading="form.loading">保存</Button>
            </Form>
        </div>
    </Poptip>

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
            save() {
                this.form.loading = true;
                this.form.data.goodsPropertyId = this.goodsProperty.id;
                this.$refs.form.validate((valid) => {
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
            remove(item) {
                this.$Modal.confirm({
                    title: '系统提示',
                    content: `您确认删除【${item.name}】吗？`,
                    onOk: () => {
                        this.$Message.info('Clicked ok');
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
                })
            },
            onCheck(item) {
                if(this.$refs[item.id][0].isChecked) {
                    if(!this.tag.checked.some((id)=>item.id === id))
                        this.tag.checked.push(item.id);
                } else {
                    if(this.tag.checked.some((id)=>item.id === id))
                        this.tag.checked.splice(this.tag.checked.findIndex((id)=>item.id === id), 1);
                }
                this.$emit('on-check', this.tag.checked)
            }
        }
    }
</script>