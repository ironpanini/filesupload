<template>
   <div>
    <input type="file" @change="handleFileChange" />
    <el-button type="primary" size="mini" @click="handleUpload">上传<i class="el-icon-upload el-icon--right"></i></el-button>
  </div>
</template>

<script>
const Length = 10;

export default {
  data: () => ({
    container: {
      file: null
    },
    data: []
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
    createFileChunk(file, length = Length) {
      const fileChunkList = [];
      const chunkSize = Math.ceil( file.size / length );
      let cur = 0;
      while (cur < file.size) {
        fileChunkList.push({file:file.slice(cur,cur + chunkSize)});
        cur += chunkSize;
      }
      return  fileChunkList
    },
    //选择文件
    handleFileChange(e) {
      const [file] = e.target.files;
      if (!file) return;
      Object.assign(this.$data, this.$options.data());
      this.container.file = file;
    },
    //点击上传
    async handleUpload() {
      if (!this.container.file) return;
      const fileChunkList = this.createFileChunk(this.container.file);
      this.data = fileChunkList.map(({file},index) => ({
        chunk: file,
        hash: this.container.file.name + '_' + index
      }));
      //window.console.log(this.data)
      await this.uploadChunks();
    },
    //上传切片
    async uploadChunks() {
      const requestList = this.data
        .map(({chunk,hash}) => {
          const formData = new FormData();
          formData.append("chunk", chunk);
            formData.append("hash", hash);
            formData.append("filename", this.container.file.name);
            return { formData };
        })
        .map(async ({formData}) => 
          this.request({
            url: "http://localhost:8080",
            data: formData
          })
        );  
        await Promise.all(requestList);
        await this.mergeRequest();
    },
    //合并切片
    async mergeRequest() {
      await this.request({
        url: "http://localhost:8080/merge",
        headers: {
          "content-type": "application/json"
        },
        data: JSON.stringify({
          filename: this.container.file.name
        })
      });
    }


  }
};

</script>

<style scoped>
div input {
  border: #ccc solid 1px;
}
</style>