<template>
  <div class="upload">
    <el-upload
      action="#"
      list-type="picture-card"
      v-if="!imgUrl"
      :before-upload="beforeUpload"
      :http-request="httpRequest"
    >
      <i class="el-icon-plus"></i>
    </el-upload>
    <div class="imgbox" v-else @click="openPic">
      <img :src="imgUrl" alt="" />
    </div>
  </div>
</template>

<script>
import Cos from "cos-js-sdk-v5";
export default {
  props: ["imgUrl"],
  data() {
    return {
      picShow: false,
      cos: null,
      percentage: 0,
      proShow: false,
    };
  },
  created() {
    this.cos = new Cos({
      SecretId: "AKIDZy7k7D2btue7KGuHNDm57sMZVBZgW5NO", // 身份识别 ID
      SecretKey: "lFTQ6VeW7RUGlzvay4tRiiRbV8TXLr63", // 身份密钥
    });
  },
  methods: {
    httpRequest(file) {
      //   console.log(file);
      this.proShow = true;
      this.cos.putObject(
        {
          Bucket: "hr-1302816769" /* 必须 */,
          Region: "ap-hongkong" /* 存储桶所在地域，必须字段 */,
          Key: file.file.name /* 必须 */,
          StorageClass: "STANDARD",
          Body: file.file, // 上传文件对象
          onProgress: (progressData) => {
            // progressData: 上传过程中的实时进度对象;
          },
        },
        (err, data) => {
          // data: 上传成功后的返回值;
          // err: 上传出错的错误信息;
          // console.log(err || data);
          //   console.log(err);
          console.log(data);
          this.$emit("update:imgUrl", "https://" + data.Location);
          setTimeout(() => {
            this.proShow = false;
            this.percentage = 0;
          }, 1000);
        }
      );
    },
    beforeUpload(file) {
      console.log(file);
      const isType = file.type == "image/jpeg" || file.type == "image/png";
      if (!isType) {
        this.$message.error("您上传的图片格式不正确");
        return fale;
      }
      const isSize = file.size < 2 * 1024 * 1024;
      if (!isSize) {
        this.$message.error("图片大小不能超过2M");
        return false;
      }
      return true;
    },
    openPic() {
      console.log(111);
      this.picShow = true;
    },
    delPic() {
      this.$emit("update:imgUrl", "");
    },
  },
};
</script>

<style lang="scss">
.imgbox {
  width: 100px;
  height: 60px;
  position: relative;
  img {
    width: 100%;
    height: 100%;
    object-fit: contain;
  }
  i {
    position: absolute;
    top: 0;
    right: 0;
    display: none;
    cursor: pointer;
  }
  &:hover i {
    display: block;
  }

  .box {
    width: 100%;
    height: 100%;
    position: fixed;
    background-color: rgba(255, 255, 255, 0.7);
  }
}
</style>