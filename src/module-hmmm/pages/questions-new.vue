<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card shadow="never">
        <div class="card_header">试题录入</div>
        <div class="card_form">
          <el-form label-width="100px" :model="form" :rules="rules" ref="form">
            <!-- 学科 -->
            <el-form-item label="学科：" prop="subjectID">
              <el-select v-model="form.subjectID" @change="getcatalogtags">
                <el-option
                  v-for="(item, index) in disciplineList"
                  :key="index"
                  :label="item.label"
                  :value="item.value"
                >
                </el-option>
              </el-select>
            </el-form-item>
            <!-- 目录 -->
            <el-form-item label="目录：" prop="catalogID">
              <el-select v-model="form.catalogID">
                <el-option
                  v-for="(item, index) in directoryList"
                  :key="index"
                  :label="item.label"
                  :value="item.value"
                >
                </el-option>
              </el-select>
            </el-form-item>
            <!-- 企业 -->
            <el-form-item label="企业：" prop="enterpriseID">
              <el-select v-model="form.enterpriseID">
                <el-option
                  v-for="item in enterpriseList"
                  :key="item.id"
                  :label="item.company"
                  :value="item.id"
                >
                </el-option>
              </el-select>
            </el-form-item>
            <!-- 城市 -->
            <div class="form_city">
              <el-form-item label="城市：" prop="province">
                <el-select
                  v-model="form.province"
                  placeholder="请选择"
                  @change="provinceChange"
                  class="province-select"
                >
                  <el-option
                    v-for="(item, index) in provincesList"
                    :key="index"
                    :label="item"
                    :value="item"
                  ></el-option>
                </el-select>
              </el-form-item>
              <el-form-item prop="city" class="city_select">
                <el-select v-model="form.city" placeholder="请选择">
                  <el-option
                    v-for="(item, index) in citysList"
                    :key="index"
                    :label="item"
                    :value="item"
                  ></el-option>
                </el-select>
              </el-form-item>
            </div>
            <!-- 方向 -->
            <el-form-item label="方向：" prop="direction">
              <el-select v-model="form.direction">
                <el-option
                  v-for="item in directionList"
                  :key="item"
                  :label="item"
                  :value="item"
                >
                </el-option>
              </el-select>
            </el-form-item>
            <!-- 题型 -->
            <el-form-item label="题型：" prop="questionType">
              <el-radio-group
                v-model="form.questionType"
                @change="questionTypeChange"
              >
                <el-radio
                  :label="item.value"
                  v-for="item in questionTypeList"
                  :key="item.value"
                  >{{ item.label }}</el-radio
                >
              </el-radio-group>
            </el-form-item>
            <!-- 难度 -->
            <el-form-item label="难度：" prop="difficulty">
              <el-radio-group
                v-model="form.difficulty"
                @change="difficultyListChange"
              >
                <el-radio
                  :label="item.value"
                  v-for="item in difficultyList"
                  :key="item.value"
                  >{{ item.label }}</el-radio
                >
              </el-radio-group>
            </el-form-item>
            <!-- 题干 -->
            <el-form-item label="题干：" prop="question">
              <quillEditor
                v-model="form.question"
                @blur="question"
                :options="editorOption"
              ></quillEditor>
            </el-form-item>
            <!-- 选项 -->
            <el-form-item
              label="选项："
              prop="options"
              class="formOptions"
              v-if="form.questionType != 3"
            >
              <div
                class="form_options"
                v-for="(item, index) in form.options"
                :key="index"
              >
                <el-radio
                  :label="true"
                  v-model="item.isRight"
                  v-if="form.questionType == 1"
                  @change="changeRadio(index)"
                  >{{ item.code + ':' }}</el-radio
                >
                <el-checkbox v-else v-model="item.isRight">
                  {{ item.code + ':' }}
                </el-checkbox>
                <el-input v-model="item.title" class="options_input"></el-input>
                <!-- <el-upload
                  class="upload-demo options_img"
                  drag
                  :action="item.img"
                  multiple
                >
                  <div class="el-upload__text">上传图片</div>
                </el-upload>
                <i class="el-icon-close"></i> -->
                <imageupload
                  class="options_img"
                  :imgUrl.sync="item.img"
                ></imageupload>
              </div>
            </el-form-item>
            <!-- 增加选项与答案 -->
            <el-form-item v-if="form.questionType != 3">
              <el-button
                type="danger"
                :disabled="form.questionType != 2"
                @click="addOptions"
                ><i class="el-icon-plus"></i>增加选项与答案</el-button
              >
            </el-form-item>
            <!-- 解析视频： -->
            <el-form-item label="解析视频：">
              <el-input v-model="form.videoURL"></el-input>
            </el-form-item>
            <!-- 答案解析 -->
            <el-form-item label="答案解析：" prop="answer">
              <quillEditor
                v-model="form.answer"
                @blur="question"
                :options="editorOption"
              ></quillEditor>
            </el-form-item>
            <!-- 题目备注 -->
            <el-form-item label="题目备注：">
              <el-input type="textarea" :rows="4" v-model="form.remarks">
              </el-input>
            </el-form-item>
            <!-- 试题标签 -->
            <el-form-item label="试题标签：" prop="tags">
              <el-select v-model="form.tags" multiple placeholder="请选择">
                <el-option
                  v-for="item in tagsList"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                >
                </el-option>
              </el-select>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="submit" v-if="!$route.query.id"
                >确认提交</el-button
              >
              <el-button type="success" @click="submit" v-else
                >确认修改</el-button
              >
            </el-form-item>
          </el-form>
        </div>
      </el-card>
    </div>
  </div>
</template>

<script>
import { quillEditor } from 'vue-quill-editor'
import 'quill/dist/quill.core.css'
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'
import { provinces, citys } from '@/api/hmmm/citys'
import { simple as discipline } from '@/api/hmmm/subjects' // 学科简单列表
import { simple as directory } from '@/api/hmmm/directorys' // 目录简单列表
import { list as enterprise } from '@/api/hmmm/companys' // 企业管理列表
// import { simple as tabs } from '@/api/hmmm/tags' // 试题标签
import {
  add as addForm,
  update as updateForm,
  detail as detailForm
} from '@/api/hmmm/questions' // 题库添加 题库修改 题库详情
import { direction, difficulty, questionType } from '@/api/hmmm/constants' // 目录数据 难度列表 方向列表
// import citys from "./companys/citys.vue";
export default {
  components: {
    quillEditor,
    // citys,
    imageupload: () => import('../components/imageupload.vue')
  },
  data () {
    return {
      editorOption: {
        modules: {
          toolbar: [
            ['bold', 'italic', 'underline', 'strike'],
            [{ list: 'ordered' }, { list: 'bullet' }],
            ['blockquote', 'code-block'],
            ['image', 'link']
          ]
        }
      },

      disciplineList: [], // 学科数据
      directoryList: [], // 目录数据
      enterpriseList: [], // 企业数据
      provincesList: provinces(), // 城市  市数据
      citysList: [], // 城市 区数据
      directionList: direction, // 企业方向
      questionTypeList: questionType, // 题型列表
      difficultyList: difficulty, // 难度列表
      tagsList: [], // 试题标签
      // citysList: "",
      num: '',
      form: {
        number: '', // 试题编号
        subjectID: '', // 学科
        catalogID: '', // 目录
        enterpriseID: '', // 企业
        // city: "", //城市
        province: '', // 市
        city: '', // 区
        direction: '', // 方向
        questionType: 1, // 题型
        difficulty: 1, // 难度
        question: '', // 题干
        options: [
          {
            code: 'A', // 代码
            title: '', // 标题
            img: '', // 图片url
            isRight: true // 是否正确答案
          },
          {
            code: 'B', // 代码
            title: '', // 标题
            img: '', // 图片url
            isRight: false // 是否正确答案
          },
          {
            code: 'C', // 代码
            title: '', // 标题
            img: '', // 图片url
            isRight: false // 是否正确答案
          },
          {
            code: 'D', // 代码
            title: '', // 标题
            img: '', // 图片url
            isRight: false // 是否正确答案
          }
        ], // 选项
        videoURL: '', // 视频
        answer: '', // 答案解析
        remarks: '', // 题目备注
        tags: '', // 试题标签
        isPerfect: '' // 后台精选
      },
      rules: {
        subjectID: [{ required: true, message: '请选择学科', trigger: 'blur' }],
        catalogID: [{ required: true, message: '请选择目录', trigger: 'blur' }],
        enterpriseID: [
          { required: true, message: '请选择企业', trigger: 'blur' }
        ],
        province: [{ required: true, message: '请选择城市', trigger: 'blur' }],
        direction: [{ required: true, message: '请选择方向', trigger: 'blur' }],
        questionType: [
          { required: true, message: '请选择题型', trigger: 'blur' }
        ],
        difficulty: [
          { required: true, message: '请选择难度', trigger: 'blur' }
        ],
        question: [
          { required: true, message: '请输入题干', trigger: 'blur' }
          // { min: 3, max: 5, message: "长度在 3 到 5 个字符", trigger: "blur" },
        ],
        answer: [{ required: true, message: '请输入答案解析', trigger: 'blur' }]
      }
    }
  },
  methods: {
    // 获取学科数据
    async getdisciplineList () {
      const { data: res } = await discipline(this.form)
      this.disciplineList = res
    },
    // async getmulu() {
    //   let { data: res } = await directory();
    //   this.directoryList = res;
    //   this.form.catalogID = "";
    //   console.log(res);
    // },
    // async getbq() {
    //   let { data: res1 } = await tabs();
    //   this.tagsList = res1;
    //   this.form.tags = null;
    // },
    // 根据学科获取目录数据和标签数据
    async getcatalogtags () {
      // console.log(this.form);
      const { data: res } = await directory(this.form)
      this.directoryList = res
      this.form.catalogID = ''
      // let { data: res1 } = await tabs(this.form);
      // this.tagsList = res1;
      // this.form.tags = null;
    },
    // 获取企业数据
    async getenterpriseList () {
      const { data: res } = await enterprise()
      this.enterpriseList = res.items
      // console.log(res.items);
    },
    // 引用城市citys的方法拿到的的数值
    provinceChange (value) {
      // console.log(citys(value));
      this.citysList = citys(value)
    },
    // 题型
    // 点击单选多选出现的问题。需要初始化选项框
    questionTypeChange (value) {
      // console.log(value);
      // 当改变题型时初始化选项
      if (value) {
        // == 1为单选
        this.form.options.splice(4) // 截取前四个选项
        this.form.options.forEach(element => {
          // 遍历数组
          element.isRight = false // 让所有按钮都取消选中
        })
        this.form.options[0].isRight = true // 默认第一个选中
      }
    },
    // 点击单选框，改变正确答案的值
    changeRadio (value) {
      // 传index过来用value接收
      // console.log(111);
      this.form.options.forEach((element, index) => {
        // 遍历options里的数据
        // 如果index和value不等则改为false，相等则为true
        index !== value ? (element.isRight = 0) : (element.isRight = 1)
      })
    },
    // 修改难度列表时更新数据
    difficultyListChange (value) {
      this.difficultyList = value
    },
    // 题干富文本事件函数启动校验
    question () {
      // validateField表单的字段验证的方法
      this.$refs.form.validateField('question')
      this.$refs.form.validateField('parsing')
    },
    // 点击增加选项与答案
    addOptions () {
      // //String.fromCharCode 将 Unicode 编码转为一个字符:
      // const code = String.fromCharCode(this.form.options.length + 65);
      // //转为字符添加到新数组
      // const newOptions = { code, title: "", img: "", isRight: false };
      // // 把新对像追加到原来的对象
      // this.form.options.push(newOptions);
      if (this.form.options.length < 26) {
        const code = String.fromCharCode(this.form.options.length + 65)
        const newOptions = { code, title: '', img: '', isRight: false }
        this.form.options.push(newOptions)
      } else {
        this.$message.error('没有更多字母了')
      }
    },
    // 获取试题标签
    // async gettagsList() {
    //   let res = await tabs();
    //   // console.log(res);
    //   this.tagsList = res.data;
    // },
    submit () {
      const newForm = JSON.parse(JSON.stringify(this.form))
      newForm.questionType === 3 && (newForm.options = [])
      newForm.questionType += ''
      newForm.difficulty += ''
      // this.form.options = this.form.options.toString();
      if (!this.form.tags.length) {
        // 当数组为空时,this.form.tags = ''为空字符串
        this.form.tags = ''
      } else {
        this.form.tags = this.form.tags.toString()
      }
      // this.form.tags = JSON.stringify(this.form.tags);
      newForm.tags = JSON.stringify(newForm.tags)
      console.log(newForm)
      this.$refs.form.validate(async valid => {
        if (!this.$route.query.id) {
          if (valid) {
            await addForm(newForm)
            // console.log(res);
            this.$message.success('新增试题成功')
            this.$router.push('/questions/list')
          } else {
            this.$message.error('请输入必填项')
          }
        } else {
          if (valid) {
            const res = await updateForm(newForm)
            console.log(res)
            this.$message.success('修改试题成功')
            this.$router.push('/questions/list')
          } else {
            this.$message.error('请输入必填项')
          }
        }
      })
    },
    async getdetailForm () {
      if (this.$route.query.id) {
        // const id = this.$route.query.id;
        const { data: res } = await detailForm(this.$route.query)
        res.questionType = res.questionType / 1
        res.difficulty = res.difficulty / 1
        // if (!res.tags) {
        //   res.tags = res.tags.split(",");
        // }
        // res.options.reverse();
        // res.options.sort();
        res.options.forEach(ele => {
          ele.isRight = Boolean(ele.isRight)
        })
        res.options.sort((a, b) => {
          return a.code.charCodeAt() - b.code.charCodeAt()
        })
        // if (!res.tags) {
        //   res.tags =
        //     res.tags.length === 0 ? "" : res.tags.split(",").map(Number);
        // } else {
        //   return false;
        // }
        this.form = res
        console.log(this.$route.query)
        console.log(this.form)
      } else {
        return false
      }
    }
  },
  mounted () {
    this.getdisciplineList()
    this.getenterpriseList()
    // this.gettagsList();
    this.getdetailForm()
  },
  created () {
    // this.getdetailForm();
    // this.getmulu();
    // this.getdisciplineList();
    // this.getdisciplineList();
  },
  watch: {
    $route (to, from) {
      // 重新获取数据
      this.$router.go(0)
    }
  }
}
</script>

<style lang="scss" scoped>
.el-card {
  .card_header {
    height: 30px;
    border-bottom: 1px solid #ebeef5;
  }
  .card_form {
    // margin: 10px 30px;
    margin-left: 70px;
    margin-top: 20px;
    .el-select {
      width: 400px;
    }
    .el-input,
    .el-textarea {
      width: 400px;
    }
    ::v-deep .ql-editor {
      height: 300px;
    }
    .province-select {
      width: 198px;
      margin-right: -10px !important;
    }
    .form_city {
      display: flex;
      .city_select {
        ::v-deep .el-form-item__content {
          margin-left: 15px !important;
          .el-input {
            width: 198px;
          }
        }
      }
    }
    .formOptions {
      display: flex;
      align-items: center;
      ::v-deep .el-form-item__content {
        margin-left: 0px !important;
      }
      .form_options {
        display: flex;
        align-items: center;
        position: relative;
        i {
          position: absolute;
          top: -5px;
          right: -5px;
          z-index: 9999;
          // color: red;
          border: 1px solid #999;
          border-radius: 50%;
          cursor: pointer;
          &:hover {
            border: 1px solid red;
            color: red;
          }
        }
        ::v-deep {
          .options_input {
            margin: 0 10px;
          }
          .options_img {
            margin: 10px 0;
            // .el-upload-dragger {
            // //   width: 100px;
            // //   height: 60px;
            // // }
            // // .el-upload-dragger {
            // //   .el-upload__text {
            // //     // line-height: 60px;
            // //   }
            // // }
          }
        }
      }
    }
  }
}
</style>
