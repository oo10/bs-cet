
<template>
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
                    登录
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
            <p>姓名：{{findData.userInfo.name}}</p>
            <p>学校：{{findData.userInfo.school}}</p>
            <p>考试类别：{{findData.userInfo.name}}</p>
            <p>准考证号：{{findData.userInfo.num}}</p>
            <p>考试时间：{{findData.userInfo.time}}</p>
        </Card>
        <Card :bordered="true">
            <p slot="title">继续努力</p>
            <p>总分：{{findData.userInfo.name}}</p>
            <p>听力：{{findData.userInfo.school}}</p>
            <p>阅读：{{findData.userInfo.name}}</p>
            <p>写作：{{findData.userInfo.num}}</p>
        </Card>
    </div>
</template>

<style scoped>
    .result {
        width: 5.5rem;
        text-align: center;
        margin-left: 1rem;
    }
    .form {
        width: 5.5rem;
        text-align: center;
        margin-left: 1rem;
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
                findData:{

                }
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
                        this.$Message.error('表单验证失败!');
                    }
                })
            },
            handleReset (name) {
                this.$refs[name].resetFields();
            },
            getData(){
                let data = '{"ticket":"'+this.formInline.password+'","name":"'+this.formInline.user+'","vCode":"","_url":"getScore"}'
                let req =  new XMLHttpRequest();
                if(req){
                    var _this = this
                    console.log(this.data)
                    req.open("POST", "http://cet.yunban.com/webservice.php", true);
                    req.setRequestHeader("Content-Type","application/x-www-form-urlencoded; charset=gbk;");
                    req.send(data);
                    req.onreadystatechange = function(){
                        if(req.readyState == 4){
                            if(req.status == 200){
                                _this.$Message.success('查询成功!');
                                _this.show = true
                                _this.findData = req.result
                                console.log(req.result)
                            }else{
                                _this.$Message.error('网络超时!');
                            }
                        }
                    }
                }
            },
        }
    }
</script>
