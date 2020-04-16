<template>
  <el-form-item v-if="!loading" v-loading="loading" :label="label+':'"
                :class="{'is-required':!noReq,'toLongla':label.length>7}" class="width100 shitImgMe" prop="businessLicense"
                style="width: 100%">
    <el-upload

            class="upload-demo"
            :action="url"
            :before-upload="beforeUploadHandle"
            :on-remove="handleRemove"
            :on-success="successHandle"
            :limit="limit||1"
            :on-exceed="upSize"
            :file-list="fileLists||[]"
            list-type="picture"
            multiple
    >
    <div class="flexd btnsa">
      
      <el-button size="small" class="f-16" type="">+</el-button>
      <div slot="tip" class="el-upload__tip">{{isDOC? (isDOC==1?'只能上传PDF文件，且文件大于100K，小于5MB':'只能上传图片或PDF文件'):'请上传jpg/png/pdf图片类文件，且文件大于100K，小于5MB'}}</div>

    </div>
     </el-upload>
  </el-form-item>
</template>
<script>
  import API from '@/api'
  import {setTimeout} from 'timers';

  export default {
    props: ['limit', 'fileList', 'label', 'keys', 'noReq', 'isDOC'],
    computed: {
      url() {
        return API.oss.upload(this.$cookies.get('token'))
      },
    },
    created() {


    },
    mounted() {
      setTimeout(() => {
        if (this.fileList) {
          let List = this.fileList.split(","), novaList = [];

          List.map((r) => {
            if (r.split('url:')[1]) {
              novaList.push({
                name: r.split('url:')[0],
                url: r.split('url:')[1]

              })
            }

          });
          this.fileLists = novaList
        }
        ;
        this.loading = false
      }, 800)
    },
    data() {
      return {
        fileLists: [],
        loading: true
      }
    },
    methods: {
      upSize(files, fileList) {
        this.$message.error("图片数量超过限制");
      },
      handleRemove(response, fileList, file) {
        console.log(response,'response')
        let list = [], that = this;
        fileList.map((r) => {
          list.push(r.name + "url:" + (r.response ? r.response.url : r.url))
        })

        this.$emit("handle", {fileList: list.join(","), key: that.keys})
      },
      successHandle(response, file, fileList) {
        console.log(response,'response')
        let list = [], that = this;
        fileList.map((r) => {
          list.push(r.name + "url:" + (r.response ? r.response.url : r.url))
        })
        this.$emit("handle", {fileList: list.join(","), key: this.keys})
      },
      beforeUploadHandle(file) {
        // console.log(file)
        // console.log(!this.isDOC ,1)
        if (!this.isDOC) {
          console.log(2)
          // if (file.type.indexOf('image') == -1 || (file.name.indexOf('jpg')==-1 && file.name.indexOf('png')==-1)) {
          if (file.type.indexOf('pdf') == -1 && file.type.indexOf('image') == -1 && (file.name.indexOf('jpg')==-1 && file.name.indexOf('png')==-1)) {
            console.log(3)
            this.$message.error('只支持jpg、png、pdf格式的文件！')
            return false
          }
          // if (file.type === 'ai/image/jpg' && file.type === 'image/png' && file.size / 1024 / 1024 / 1024 < 500) {
          //   this.$message.error('文件大于500K')
          //   return false
          // }
          
        } else {
          if(this.isDOC==1){

            if (file.type.indexOf('pdf') == -1) {
              this.$message.error('只支持PDF格式的文件！')
              return false
            }
          }else{
            if (file.type.indexOf('pdf') == -1 && (file.type.indexOf('image') == -1||(file.type.indexOf('image') != -1&&(file.name.indexOf('jpg')==-1&&file.name.indexOf('png')==-1)))) {
              this.$message.error('只支持PDF、jpg、png格式的文件！')
              return false
            }
          }
        }

        if (file.size / 1024 / 1024 > 5) {
            this.$message.error('文件大于5M')
            return false
          }
        if (file.size / 1024  < 100) {
            this.$message.error('文件小于100K')
            return false
        }


      }
    },
  }
</script>
<style lang="less">

.shitImgMe{
  .upload-demo{
    max-width: 100%;
  }
  .btnsa{
    align-items: center;
    padding: 4px 6px;
    padding-right: 30px;
    width: 450px;
    border:1px dashed #DEDEDE;
    border-radius: 3px;
    &:hover{
      background-color: #ecf5ff;
        .el-button--small{
           background-color: #ecf5ff;
        }
    }
      .el-button--small{
            font-weight: bold;
    font-size: 24px!important;
    border: none!important;
    padding: 2px;
    margin-left: 4px
      }
      .el-upload__tip{
        margin-top: 0;
        margin-left: 12px;

      }

  }
}
</style>
