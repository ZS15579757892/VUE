<template>
    <el-container>
        <el-header style="padding: 0;">
            <el-menu class="el-menu-demo" mode="horizontal" :ellipsis="false">
                <el-sub-menu index="2">
                    <template #title>
                        <el-icon size="20">
                            <UserFilled />
                        </el-icon>
                    </template>
                    <el-menu-item @click="logout" index="2-1">退出登入</el-menu-item>
                </el-sub-menu>
                <el-menu-item index="0"><b>图书管理</b></el-menu-item>
                <!-- <el-menu-item @click="logout" index="0"><b>退出登录</b></el-menu-item> -->
                <div class="flex-grow" />
            </el-menu>
        </el-header>
        <el-main>
            <el-button @click="dlgAddShow = true" icon="Plus" type="primary" plain> 添加图书 </el-button>
            <el-card>
                <el-table :data="books" style="width: 100%">
                    <el-table-column label="图书编号" prop="id" />
                    <el-table-column label="书名" prop="name" />
                    <el-table-column label="是否借出">
                        <template #default="scope">
                            <el-tag type="danger" v-if="scope.row.status">已借出</el-tag>
                            <el-tag type="success" v-else>未借出</el-tag>
                        </template>
                    </el-table-column>
                    <el-table-column>
                        <template #default="scope">
                            <el-button type="primary" size="small">出借</el-button>
                            <el-button type="primary" size="small">归还</el-button>
                            <el-button @click="clickDel(scope.row.id)" icon="Delete" size="small"
                                type="danger">删除</el-button>
                        </template>
                    </el-table-column>
                </el-table>
            </el-card>
        </el-main>
    </el-container>

    <!-- 添加图书对话框 -->
    <el-dialog v-model="dlgAddShow" title="添加图书" width="30%">
        <el-form :model="addData">
            <el-form-item label="书籍编号">
                <el-input v-model="addData.id" autocomplete="off" />
            </el-form-item>
            <el-form-item label="书籍名称">
                <el-input v-model="addData.name" autocomplete="off" />
            </el-form-item>
        </el-form>
        <template #footer>
            <span class="dialog-footer">
                <el-button @click="dlgAddShow = false">取消</el-button>
                <el-button @click="saveAddData" type="primary">确认</el-button>
            </span>
        </template>
    </el-dialog>
</template>


<script>
import { ElMessage, ElMessageBox } from 'element-plus'
export default {
    data() {
        return {
            // 添加数据输入
            addData: {
                id: '',
                name: ''
            },
            // 是否显示添加的窗口
            dlgAddShow: false,
            books: []
        }
    },
    methods: {
        async getAllBook() {
            const response = await this.$api.getBooks()
            this.books = response.data.data
        },
        async delBook(id) {
            const response = await this.$api.delBook(id)
            if (response.data.code === 1000) {
                // 更新前端数据
                this.getAllBook()
                ElMessage({
                    type: 'success',
                    message: "删除成功"
                })
            } else {
                ElMessage({
                    type: 'error',
                    message: response.data.message
                })
            }
        },
        clickDel(id) {
            //给出确认提示
            ElMessageBox.confirm(
                '删除之后不可恢复，请确认是否执行该操作?',
                '提示',
                {
                    confirmButtonText: '确认',
                    cancelButtonText: '取消',
                    type: 'warning',
                }
            )
                .then(() => {
                    // 点击确认，调用操作进行删除
                    this.delBook(id)
                })
                .catch(() => {
                    //点击取消
                    ElMessage({
                        type: 'info',
                        message: '已取消删除',
                    })
                })
        },
        async saveAddData() {
            const response = await this.$api.addBook(this.addData)
            if (response.data.code === 1001) {
                ElMessage({
                    type: 'success',
                    message: "添加成功"
                })
                // 刷新页面
                this.getAllBook()
                //关闭添加窗口
                this.dlgAddShow = false
            } else {
                ElMessage({
                    type: 'error',
                    message: response.data.message
                })
            }
        },
        async logout() {
            const response = await this.$api.logout()
            if (response.data.code === 1000) {
                ElMessage({
                    type: 'success',
                    message: response.data.message
                })
                this.$router.push('/login')
            } else {
                ElMessage({
                    type: 'error',
                    message: "未退出成功"
                })
            }
        }
    },
    created() {
        // 调用后端接口获取图书
        this.getAllBook()
    },

}

</script>

<style></style>