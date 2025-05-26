<template>
  <div class="upload-container">
    <h2>文件上传</h2>

    <!-- 文件选择 -->
    <input type="file" multiple @change="handleFileChange" />

    <!-- 已选择的文件列表 -->
    <ul class="file-list" v-if="selectedFiles.length > 0">
      <li v-for="(file, index) in selectedFiles" :key="index">
        {{ file.name }}
        <button @click="removeFile(index)">删除</button>
      </li>
    </ul>

    <!-- 上传按钮 -->
    <button :disabled="selectedFiles.length === 0" @click="uploadFiles">上传文件</button>

    <!-- 上传结果 -->
    <p v-if="uploadStatus">{{ uploadStatus }}</p>
  </div>
</template>

<script>
export default {
  name: 'Upload',
  data() {
    return {
      selectedFiles: [],
      uploadStatus: '',
    };
  },
  methods: {
    // 当用户选择文件时触发
    handleFileChange(event) {
      this.selectedFiles = Array.from(event.target.files);
    },

    // 删除已选择的某个文件
    removeFile(index) {
      this.selectedFiles.splice(index, 1);
    },

    // 执行上传操作
    async uploadFiles() {
      this.uploadStatus = '上传中...';

      const formData = new FormData();
      this.selectedFiles.forEach((file) => {
        formData.append('file', file); // 根据后端需求修改字段名
      });

      try {
        const response = await fetch('/api/upload', {
          method: 'POST',
          body: formData,
        });

        if (response.ok) {
          this.uploadStatus = '上传成功！';
          this.selectedFiles = [];
        } else {
          this.uploadStatus = '上传失败。';
        }
      } catch (error) {
        this.uploadStatus = '网络错误，上传失败。';
        console.error(error);
      }
    },
  },
};
</script>

<style scoped>
.upload-container {
  padding: 20px;
  max-width: 600px;
  margin: 0 auto;
  font-family: Arial, sans-serif;
}

.file-list {
  list-style: none;
  padding: 0;
  margin-top: 10px;
}

.file-list li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 5px;
  background: #f2f2f2;
  padding: 5px 10px;
  border-radius: 4px;
}

button {
  margin-left: 10px;
  padding: 5px 10px;
  background-color: #1976d2;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}
</style>