<template>
<div>
  <el-drawer
    title="编辑子项信息"
    :visible.sync="isShowDrawer"
    direction="rtl">
    <div class="drawer-content">
      <el-form
        ref='editItemForm'
        :model='editItemForm'
        :rules='editItemRules'>
        <el-form-item
          label="子项名称"
          prop='title'
          label-width="80px">
          <el-input
            type='text'
            v-model="editItemForm.title"
            maxlength='30'
            show-word-limit
            autocomplete="off"
          ></el-input>
        </el-form-item>
        <el-form-item
          label="子项描述"
          prop='description'
          label-width="80px">
          <el-input
            type='textarea'
            v-model="editItemForm.description"
            maxlength='300'
            show-word-limit
          ></el-input>
        </el-form-item>
        <el-form-item
          label='子项类别'
          prop='category'
          label-width='80px'>
          <el-select
            v-model="editItemForm.category"
            placeholder="请选择子项类别">
            <el-option label="生活类" value="0"></el-option>
            <el-option label="工作类" value="1"></el-option>
            <el-option label="学习类" value="2"></el-option>
            <el-option label="其他" value="3"></el-option>
          </el-select>
        </el-form-item>
      </el-form>
    </div>
    <div class="drawer-footer" style="padding-left:30px">
      <el-button
        type="primary"
        @click="saveEditItemForm('editItemForm')"
      >保存</el-button>
      <el-button @click="isShowDrawer=false">取消</el-button>
    </div>
  </el-drawer>
</div>
</template>

<script>
import { mapState } from 'vuex'
import { editItemRequest, getListRequest } from 'api/request'

export default {
  name: 'EditItem',
  computed: {
    ...mapState(['userid']),
  },
  inject: ['reload'],
  data() {
    return {
      isShowDrawer: false,
      editItemForm: {
        itemid: null,
        status: 0,
        title: '',
        description: '',
        category: '',
      },
      editItemRules: {
        title: [
          { required: true, message: '子项名称不能为空', trigger: 'blur' },
        ],
        description: [
          { required: true, message: '子项描述不能为空', trigger: 'blur' },
        ],
        category: [
          { required: true, message: '子项类别不能为空', trigger: 'blur' },
        ],
      },
    }
  },
  methods: {
    saveEditItemForm(formName) {
      this.$refs[formName].validate(async (valid) => {
        if (!valid) {
          return false
        }
        this.isShowDrawer = false
        const res = await editItemRequest(this.editItemForm)
        if (!res.errno) {
          this.reload()
          this.$message({
            message: '修改子项信息成功！',
            type: 'success',
          });
          return true
        }
        this.$message({
          message: '修改子项信息失败！',
          type: 'error',
        });
        return true
      })
    },
  },
  mounted() {
    this.$EventBus.$on('showEditItemDrawer', async (itemid) => {
      const rst = await getListRequest({
        userid: this.userid,
      });
      rst.data.lists.forEach((list) => {
        list.items.forEach((item) => {
          if (item.id === itemid) {
            this.editItemForm.itemid = itemid
            this.editItemForm.title = item.title
            this.editItemForm.description = item.description
            this.editItemForm.category = item.category
          }
        })
      })
      this.isShowDrawer = true
    })
  },
}
</script>

<style lang="scss" scoped>

</style>
