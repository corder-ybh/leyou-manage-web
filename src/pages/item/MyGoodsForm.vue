<template>
  <v-stepper v-model="step">
    <v-stepper-header>
      <v-stepper-step :complete="step > 1" step="1">基本信息</v-stepper-step>
      <v-divider/>
      <v-stepper-step :complete="step > 2" step="2">商品描述</v-stepper-step>
      <v-divider/>
      <v-stepper-step :complete="step > 3" step="3">规格参数</v-stepper-step>
      <v-divider/>
      <v-stepper-step step="4">SKU属性</v-stepper-step>
    </v-stepper-header>
    <v-stepper-items>
      <v-stepper-content step="1">
        基本信息
      </v-stepper-content>
      <v-stepper-content step="2">
        商品描述
      </v-stepper-content>
      <v-stepper-content step="3">
        规格参数
      </v-stepper-content>
      <v-stepper-content step="4">
        SKU属性
      </v-stepper-content>
    </v-stepper-items>
  </v-stepper>
</template>

<script>
    export default {
        name: "my-goods-form",
        props: {
          oldGoods: {
            type:Object
          },
          isEdit: {
            type: Boolean,
            default: false
          }
        },
      data() {
          return {
            step: 1, // 当前的步骤数， 默认是1
          }
      },
      methods: {

      }
    }
</script>

<style scoped>

</style>
