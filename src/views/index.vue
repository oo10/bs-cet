
<template>

    <h3 style="color:#464c5b">英语四六级成绩查询</h3>
    <div class="form">
        <i-form v-ref:form-inline :model="formInline" :rules="ruleInline" >
            <Form-item prop="user">
                <i-input type="text" :value.sync="formInline.user" placeholder="姓名">
                    <Icon type="android-person" slot="prepend"></Icon>
                </i-input>
            </Form-item>
            <Form-item prop="password">
                <i-input type="text" :value.sync="formInline.password" placeholder="准考证号">
                    <Icon type="android-favorite-outline" slot="prepend"></Icon>
                </i-input>
            </Form-item>
            <Form-item>
                <i-button type="primary" @click="handleSubmit('formInline')">
                    查询
                </i-button>
                <i-button type="ghost" @click="handleReset('formInline')" style="margin-left: 0.4rem">
                    重置
                </i-button>
            </Form-item>
        </i-form>
    </div>
    <div class="result" v-if="show">
        <Card :bordered="true">
            <p slot="title">查询结果</p>
            <p>姓名：{{result.name}}</p>
            <p>学校：{{result.school}}</p>
            <p>考试类别：{{result.name}}</p>
            <p>准考证号：{{result.num}}</p>
            <p>考试时间：{{result.time}}</p>
        </Card>
        <Card :bordered="true">
            <p>总分：{{score.totleScore}}</p>
            <p>听力：{{score.tlScore}}</p>
            <p>阅读：{{score.ydScore}}</p>
            <p>写作翻译：{{score.xzpyScore}}</p>
        </Card>
    </div>
    <div class="result"> 
        <Card :bordered="true" v-if="!!res">
            <p>{{res}}</p>
        </Card>
    </div>
</template>

<style scoped>

h3 {
    font-size: .4rem;
    text-align: center;
    margin-top: 0.2rem;
    line-height: 1rem;
}
    .result {
        width: 6rem;
        text-align: center;
        margin-left: 0.75rem;
    }
    .form {
        width: 6rem;
        text-align: center;
        margin-left: 0.75rem;
    }
    form > div {
        margin: 0 0 0.4rem 0
    }
</style>

<script>
    export default {

        directives: {
        },
        components: {
        },
        data () {
            return {
                formInline: {
                    user: '',
                    password: ''
                },
                ruleInline: {
                    user: [
                        { required: true, message: '请填写姓名', trigger: 'change' }
                    ],
                    password: [
                        { required: true, message: '请填写准考证号', trigger: 'change' },
                        { type: 'string', min: 6, message: '学号长度不正确', trigger: 'change' }
                    ]
                },
                show: false,
                result:{},
                score:{},
                res: ''
            }
        },
        ready () {

        },
        beforeDestroy () {

        },
        methods: {
            handleSubmit(name) {
                this.$refs[name].validate((valid) => {
                    if (valid) {
                        this.$Message.loading('查询中...', 0)
                        this.getData()
                    } else {
                        this.$Message.error('信息验证失败!');
                    }
                })
            },
            handleReset (name) {
                this.$refs[name].resetFields();
            },
            getData(){
                let name = this.formInline.user
                let num = this.formInline.password
                let _this = this
                $.ajax({
                    type:"GET",
                    url:"http://yangqiwang.cn/api-cet/?name="+name+"&num="+num,
                    data:{},
                    datatype: "json",            
                    success:function(req){
                        console.log(req)
                        let data = jQuery.parseJSON(req)
                        console.log(data)
                            if(data.status == 200){
                            console.log(1)
                                _this.$Message.destroy()
                                _this.$Message.success('查询成功!');
                                _this.show = true
                                _this.result = data.result
                                _this.score = data.score
                                console.log(data.result)
                                console.log(_this.result)
                            }
                            else if (data.status == 201){
                                console.log(2)
                                _this.res = data.message
                                console.log(data.result)
                                _this.$Message.destroy()
                            }
                            else{
                                _this.$Message.destroy()
                                _this.$Message.error('网络超时!');
                            }
                    },
                    error: function(){
                    }         
                });
            },
        }
    }
</script>
