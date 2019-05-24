<template>
    <div>
        <Modal v-model="showModal" :styles="{top: '20px'}">
            <p slot="header" style="color:#2d292a;">
                <Icon type="ios-hand-outline" />
                <span style="margin-left: 5px">{{ title }}</span>
            </p>
            <div style="text-align:left">
                <Form :model="temp" ref="temp" :rules="rules" :label-width="80">
                    <FormItem label="客户名称">
                        <span>{{customerName}}</span>
                    </FormItem>
                    <FormItem  label="标题" prop="title">
                        <Input v-model="temp.title" placeholder="请输入标题....."></Input>
                    </FormItem>
                    <FormItem  v-if="type=='2'" label="洽谈项目" prop="cooperation">
                        <Input v-model="temp.cooperation" placeholder="请输入洽谈项目"></Input>
                    </FormItem>
                    <FormItem v-if="type=='2'" label="洽谈金额" prop="cooperation_amount">
                        <Input type="text" v-model="temp.cooperation_amount" number placeholder="请输入洽谈金额"></Input>
                    </FormItem>
                    <FormItem label="原因" prop="reply">
                        <Input v-model="temp.reply" type="textarea" :autosize="{minRows: 2,maxRows: 5}" placeholder="请输入内容....."></Input>
                    </FormItem>
                </Form>
            </div>
            <div slot="footer">
                <Button type="info" ghost shape="circle"  @click="showModal=false">取消</Button>
                <Button type="primary" ghost shape="circle" @click="save">确定</Button>
            </div>
        </Modal>
    </div>
</template>
<script>
import { requestApi } from '../../api/check'
export default {
  name: 'audit-form-request',
  props: {
    customerId: {
      type: Number,
      default: 1
    },
    title: {
      type: String,
      default: 'title'
    },
    customerName: {
      type: String,
      default: 'default'
    }
  },
  watch: {},
  data () {
    const validateMoney = (rule, value, callback) => {
      if (!value) {
        return callback(new Error('洽谈项目金额必须输入'))
      }
      // 模拟异步验证效果
      setTimeout(() => {
        if (!Number.isInteger(value)) {
          callback(new Error('洽谈金额必须是数字'))
        } else {
          if (value < 100) {
            callback(new Error('洽谈金额必须大于99'))
          } else {
            callback()
          }
        }
      }, 1000)
    }
    return {
      showModal: false,
      type: 1,
      temp: {
        customer_id: undefined,
        type: undefined,
        title: undefined,
        reply: undefined
      },
      rules: {
        title: [
          { required: true, message: '请输入标题', trigger: 'change' }
        ],
        cooperation: [
          { required: true, message: '请输入洽谈项目', trigger: 'blur' }
        ],
        cooperation_amount: [
          { validator: validateMoney, trigger: 'blur' }
        ],
        reply: [
          // { required: true, message: '请输入内容', trigger: 'blur' },
          // { type: 'string', min: 1, message: '不能少于5个字符', trigger: 'blur' }
        ]
      }
    }
  },
  created () {
  },
  methods: {
    save () {
      this.$refs.temp.validate((valid) => {
        if (valid) {
          this.temp.type = this.type
          this.temp.customer_id = this.customerId
          const tempData = Object.assign({}, this.temp)
          requestApi(tempData).then((res) => {
            if (res.data.code === '200') {
              this.$emit('on-save-success')
              this.$Message.success(this.title + ' 成功')
            } else {
              this.$emit('on-save-success')
              this.$Notice.error({ title: this.title + ' 失败', desc: res.data.msg })
            }
          })
        } else {
          this.$Message.error('规则报错')
        }
      })
    },
    setType: function (type) {
      this.type = type
    },
    show: function () {
      this.showModal = true
    },

    hide: function () {
      this.showModal = false
    }
  }
}
</script>

<style scoped>

</style>
