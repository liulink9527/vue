<template>
    <el-card style="width: 500px">
        <el-form label-width="80px" size="small">
            <el-upload
                class="avatar-uploader"
                action="http://localhost:8080/api/file/upload"
                :show-file-list="false"
                :on-success="handleAvatarSuccess"
            >
                <img v-if="form.avatarUrl" :src="form.avatarUrl" class="avatar" />
                <i v-else class="el-icon-plus avatar-uploader-icon"></i>
            </el-upload>
            <br />
            <el-form-item label="用户名">
                <el-input v-model="form.username" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="昵称">
                <el-input v-model="form.nickname" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="邮箱">
                <el-input v-model="form.email" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="电话">
                <el-input v-model="form.phone" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="地址">
                <el-input v-model="form.address" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="角色">
                <el-select v-model="form.role" placeholder="请选择角色" style="width: 100%">
                    <el-option v-for="item in roles" :key="item.name" :label="item.name" :value="item.flag"></el-option>
                </el-select>
            </el-form-item>
            <el-form-item>
                <el-button type="primary" @click="save">确 定</el-button>
                <el-button @click="back">取 消</el-button>
            </el-form-item>
        </el-form>
    </el-card>
</template>

<script>
export default {
    name: "PersonInfo",
    data() {
        return {
            form: {},
            user: localStorage.getItem("user") ? JSON.parse(localStorage.getItem("user")) : {},
            roles: [],
        };
    },
    created() {
        this.getUser().then(res => {
            console.log(res);
            this.form = res;
        });
        this.request.get("/role").then(res => {
            this.roles = res.data;
        });
    },
    methods: {
        async getUser() {
            return (await this.request.get(`/user/findUser?username=${this.user.username}`)).data;
        },
        handleAvatarSuccess(res) {
            this.form.avatarUrl = res;
        },
        save() {
            this.request.post("/user", this.form).then(res => {
                if (res) {
                    this.$message.success("保存成功");

                    //更新浏览器存储的用户信息
                    this.getUser().then(res => {
                        res.token = JSON.parse(localStorage.getItem("user")).token;
                        localStorage.setItem("user", JSON.stringify(res));
                    });

                    this.$bus.$emit("refresh", this.form);

                    this.$router.push("/home");
                } else {
                    this.$message.error("保存失败");
                }
            });
        },
        back() {
            this.$router.push("/home");
        },
    },
};
</script>

<style>
.avatar-uploader {
    text-align: center;
}
.avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
}
.avatar-uploader .el-upload:hover {
    border-color: #409eff;
}
.avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
}
.avatar {
    width: 178px;
    height: 178px;
    display: block;
}
</style>
