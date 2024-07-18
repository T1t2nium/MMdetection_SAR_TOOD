<template>
  <div>
    <el-card>
      <h1 class="detection-title">SAR目标检测系统</h1>
      <el-row type="flex" justify="center">
        <el-col :span="4">
          <el-upload
            action="#"
            :auto-upload="false"
            :on-change="onFileChange"
            :show-file-list="false"
          >
            <el-button type="primary" class="move-right">选择图片</el-button>
          </el-upload>
        </el-col>
        <el-col :span="2">
          <el-button type="success" @click="uploadImage" class="move-right">上传并预测</el-button>
        </el-col>
      </el-row>
    </el-card>
    <el-card v-if="selectedFile" type="flex">
      <el-row type="flex" justify="center">
        <el-col :span="12">
          <h2>选择的图片</h2>
          <img :src="selectedImageSrc" alt="Selected Image" style="max-width: 50%;">
        </el-col>
        <el-col :span="6">
          <h2>预测结果</h2>
          <img :src="predictedImage" alt="Predicted Image" style="max-width: 100%;">
        </el-col>
      </el-row>
    </el-card>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      selectedFile: null,
      selectedImageSrc: null,
      predictedImage: null
    };
  },
  methods: {
    onFileChange(file) {
      this.selectedFile = file.raw;
      if (this.selectedFile) {
        this.selectedImageSrc = URL.createObjectURL(this.selectedFile);
      }
    },
    async uploadImage() {
      if (!this.selectedFile) {
        this.$message.warning('请选择一张图片');
        return;
      }

      let formData = new FormData();
      formData.append('file', this.selectedFile);

      try {
        let response = await axios.post('http://localhost:5000/predict', formData, {
          headers: {
            'Content-Type': 'multipart/form-data'
          },
          responseType: 'blob'
        });

        this.predictedImage = URL.createObjectURL(response.data);
      } catch (error) {
        this.$message.error('上传失败');
        console.error('上传失败', error);
      }
    }
  }
};
</script>

<style>
.detection-title {
  font-family: 'Arial', sans-serif; /* 更改字体 */
  font-size: 2.5em; /* 更改字体大小 */
  color: #6ca9ef; /* 更改字体颜色 */
  text-shadow: 2px 2px 4px #ccc; /* 添加阴影效果 */
  text-align: center; /* 居中对齐 */
}

/* 添加间距 */
.el-row {
  margin-bottom: 20px;
}

.el-button {
  margin-right: 10px;
}

.move-right {
  margin-right: 20px; /* 调整右外边距 */
}
</style>
