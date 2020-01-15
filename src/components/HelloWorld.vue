<template>
   <div>
    <input type="file" @change="handleFileChange" />
    <el-button @click="handleUpload">上传</el-button>
  </div>
</template>

<script>
export default {
  data: () => ({
    container: {
      file: null
    }
  }),
  methods: {
    // 原生请求
    request({
      url,
      method = "post",
      data,
      headers = {},
      //requestList
    }) {
      return new Promise(resolve => {
        const xhr = new XMLHttpRequest();
        xhr.open(method, url);
        Object.keys(headers).forEach(key =>
          xhr.setRequestHeader(key, headers[key])
        );
        xhr.send(data);
        xhr.onload = e => {
          resolve({
            data: e.target.response
          });
        };
      });
    },
    // 对文件切片
    // createFileChunk(file, length) {
    //   const fileChunkList = [];
    //   fileChunkList = file 
    // },

    handleFileChange(e) {
      const [file] = e.target.files;
      if (!file) return;
      Object.assign(this.$data, this.$options.data());
      this.container.file = file;
    },
    async handleUpload() {
      
    }
  }
};

</script>

<style scoped>
div input {
  border: #ccc solid 1px;
}
</style>