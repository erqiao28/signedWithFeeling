<script setup>
import { ref, reactive } from 'vue'
import { ElMessage } from 'element-plus'
import { Paperclip } from '@element-plus/icons-vue'
import axios from 'axios'

const form = reactive({
  userName: '',
  mobile: '',
  cardNo: '',
  idCard: '',
  idCardFrontPic: [],
  idCardBackPic: [],
})

const formRef = ref(null)

const rules = {
  userName: [{ required: true, message: '请输入姓名', trigger: 'blur' }],
  mobile: [{ required: true, message: '请输入手机号', trigger: 'blur' }],
  cardNo: [{ required: true, message: '请输入银行卡号', trigger: 'blur' }],
  idCard: [{ required: true, message: '请输入身份证号', trigger: 'blur' }],
  idCardFrontPic: [ 
    { required: true, validator: (_, __, cb) => (form.idCardFrontPic?.length ? cb() : cb(new Error('请上传身份证正面照片'))), trigger: 'change' },
  ],
  idCardBackPic: [
    { required: true, validator: (_, __, cb) => (form.idCardBackPic?.length ? cb() : cb(new Error('请上传身份证反面照片'))), trigger: 'change' },
  ],
}

const submit = () => {
  formRef.value?.validate((valid) => {
    if (!valid) return

    axios
      .post('/api/workflow/hooks/Njk5ZDUyYzMwZjBkMGFkODBmNTFjZTIx', form)
      .then((res) => {
        console.log('接口返回：', res)
        const data = res.data
        // 后端约定：200 且包含 jsonString，为成功，直接跳转
        if (res.status === 200 && data?.jsonString) {
          window.location.href = data.jsonString
        } else {
          ElMessage.error(data?.message || '提交失败')
        }
      })
      .catch((err) => {
        console.error(err)
        ElMessage.error('提交失败，请稍后重试')
      })
  })
}

const handleUpload = (rawFile, field) => {
  const item = { name: rawFile.name, url: URL.createObjectURL(rawFile), raw: rawFile }
  if (field === 'front') form.idCardFrontPic = [item]
  else form.idCardBackPic = [item]
}
</script>

<template>
  <!-- 第一层：背景，两张全宽色块（40% + 60% 高度） -->
  <div class="relative min-h-screen">
    <div class="flex flex-col">
      <div class="h-[43vh] w-full bg-[#1677ff]"></div>
      <div class="h-[57vh] w-full bg-[#e6f4ff]"></div>
    </div>

    <!-- 第二层：表单卡片，覆盖在背景上，水平居中，距顶部约 200px -->
    <div class="absolute inset-0 flex justify-center px-4 md:px-8">
      <div
        class="w-full max-w-[1100px] bg-white px-4 py-5 shadow-[0_2px_10px_rgba(0,0,0,0.06)] md:px-10 md:py-25 mt-[140px] md:mt-[200px] mb-20"
      >
        <h1 class="mb-6 text-4xl font-bold text-gray-900">有感签约</h1>
        <el-form ref="formRef" :model="form" :rules="rules" label-position="top">
          <el-row :gutter="24" class="mb-1">
            <el-col :xs="24" :sm="12" :md="8">
              <el-form-item label="姓名" required prop="userName">
                <el-input v-model="form.userName" placeholder="请填写文本内容" clearable />
              </el-form-item>
            </el-col>
            <el-col :xs="24" :sm="12" :md="8">
              <el-form-item label="手机号" required prop="mobile">
                <el-input v-model="form.mobile" placeholder="请填写文本内容" clearable />
              </el-form-item>
            </el-col>
            <el-col :xs="24" :sm="12" :md="8">
              <el-form-item label="银行卡号" required prop="cardNo">
                <el-input v-model="form.cardNo" placeholder="请填写文本内容" clearable />
              </el-form-item>
            </el-col>
          </el-row>
          <el-row :gutter="24" class="mb-2">
            <el-col :xs="24" :sm="12" :md="8">
              <el-form-item label="身份证号" required prop="idCard">
                <el-input v-model="form.idCard" placeholder="请填写文本内容" clearable />
              </el-form-item>
            </el-col>
            <el-col :xs="24" :sm="12" :md="8">
              <el-form-item label="身份证正面照片" required prop="idCardFrontPic">
                <div class="flex items-center gap-3">
                  <el-upload
                    :auto-upload="false"
                    :limit="1"
                    :on-change="(uploadFile) => handleUpload(uploadFile.raw, 'front')"
                    :file-list="form.idCardFrontPic"
                    accept="image/*"
                  >
                    <el-button type="default" class="attachment-button">
                      <el-icon><Paperclip /></el-icon>
                      添加附件
                    </el-button>
                  </el-upload>
                </div>
              </el-form-item>
            </el-col>
            <el-col :xs="24" :sm="12" :md="8">
              <el-form-item label="身份证反面照片" required prop="idCardBackPic">
                <div class="flex items-center gap-3">
                  <el-upload
                    :auto-upload="false"
                    :limit="1"
                    :on-change="(uploadFile) => handleUpload(uploadFile.raw, 'back')"
                    :file-list="form.idCardBackPic"
                    accept="image/*"
                  >
                    <el-button type="default" class="attachment-button">
                      <el-icon><Paperclip /></el-icon>
                      添加附件
                    </el-button>
                  </el-upload>
                </div>
              </el-form-item>
            </el-col>
          </el-row>
          <div class="mt-3 flex justify-center">
            <el-button
              type="primary"
              size="large"
              class="submit-button min-w-[100px] bg-[#1e88e5] border-[#1e88e5] hover:bg-[#125aac] hover:border-[#125aac]"
              @click="submit"
            >
              提交
            </el-button>
          </div>
        </el-form>
      </div>
    </div>
  </div>
</template>
