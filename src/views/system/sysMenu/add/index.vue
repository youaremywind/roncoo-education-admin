<template>
  <el-dialog v-model="visible" :before-close="handleClose" :title="title" center width="600px">
    <el-form ref="form" :model="form" :rules="rules" label-width="100px">
      <el-form-item label="菜单类型" prop="menuType">
        <el-radio-group v-model="form.menuType">
          <template v-for="item in menuTypeEnumList">
            <el-radio v-if="true === item.isShow" :key="item.code" :label="item.code"> {{ item.desc }}</el-radio>
          </template>
        </el-radio-group>
      </el-form-item>
      <el-form-item v-if="info.parentName" label="上级菜单">
        <el-input v-model="info.parentName" class="form-group" disabled/>
      </el-form-item>
      <el-form-item label="菜单名称" prop="menuName">
        <el-input v-model="form.menuName" class="form-group" maxlength="50" show-word-limit/>
      </el-form-item>
      <el-form-item label="路由地址" prop="menuUrl">
        <el-input v-model="form.menuUrl" class="form-group" maxlength="50" show-word-limit/>
      </el-form-item>
      <el-form-item label="图标">
        <el-row>
          <el-col :span="12">
            <el-select v-model="form.menuIcon" clearable filterable placeholder="请选择">
              <el-option v-for="item in svgIconList" :key="item.icon" :label="item.label" :value="item.icon">
                <span style="margin-right:5px;float: left">{{ item.label }}</span>
                <span style="float: right; color: #8492a6; font-size: 13px">
              <svg-icon :icon-class="item.icon || ''"/>
            </span>
              </el-option>
            </el-select>
          </el-col>
          <el-col :span="12" style="text-align: left">
            <svg-icon :icon-class="form.menuIcon || ''" class-name="icon"/>
          </el-col>
        </el-row>
      </el-form-item>
      <el-form-item label="排序" prop="sort">
        <el-input-number v-model="form.sort" :min="0" controls-position="right"/>
      </el-form-item>
    </el-form>
    <template #footer>
      <div class="dialog-footer">
        <el-button @click="handleClose">取 消</el-button>
        <el-button type="primary" @click="submitForm('form')">确 定</el-button>
      </div>
    </template>
  </el-dialog>
</template>
<script>
import {sysMenuSave} from '@/api/system';

export default {
  name: 'AddSysMenu',
  props: {
    title: {
      type: String,
      default: null
    },
    visible: {
      type: Boolean,
      default: false
    },
    info: {
      type: Object,
      default: () => {
      }
    }
  },
  emits: ['closes'],
  data() {
    return {
      form: {
        sort: 1,
        parentId: this.info.parentId,
        menuType: 1,
        outLink: 0
      },
      opts: {},
      svgIconList: [],
      rules: {
        menuName: [
          {required: true, message: '请输入菜单名称', trigger: 'blur'}
        ],
        menuType: [
          {required: true, message: '请选择菜单类型', trigger: 'blur'}
        ],
        menuUrl: [
          {required: true, message: '请输入路由地址', trigger: 'blur'}
        ]
      },
      menuTypeEnumList: [
        {
          code: 1,
          desc: '目录',
          isShow: true,
          name: 'DIRECTORY'
        },
        {
          code: 2,
          desc: '菜单',
          isShow: true,
          name: 'MENU'
        },
        {
          code: 3,
          desc: '权限',
          isShow: false,
          name: 'PERMISSION'
        }
      ]
    };
  },
  mounted() {
    this.svgIconList = this.$store.state.user.svgIconList;
    // this.$store.dispatch('GetOpts', { enumName: 'MenuTypeEnum', type: 'arr' }).then(res => {
    //   this.menuTypeEnumList = res;
    // });
  },
  methods: {
    handleClose() {
      this.$emit('closes');
      this.form = {};
    },
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          this.onSubmit();
        } else {
          return false;
        }
      });
    },
    onSubmit() {
      this.loading.show();
      // 新增
      sysMenuSave(this.form)
        .then((res) => {
          this.loading.hide();
          this.$message.success(res, 'success');
          this.$emit('closes', this.form);
        })
        .catch(() => {
          this.loading.hide();
        });
    }
  }
};
</script>
<style scoped>
.icon {
  font-size: 24px;
  margin-top: 5px;
}
</style>
