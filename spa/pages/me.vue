<template>
  <loading :loading="loading" :fullscreen="false">
    <v-container v-if="me">
      <v-flex class="mb-4">
        <h4 class="mb-2 caption">个人信息</h4>
        <p class="body-2">用户名：{{ me.name }}</p>
      </v-flex>
      <v-flex class="mb-4">
        <h4 class="mb-2 caption">实名信息</h4>
        <p class="body-2">实名状态：{{ me.kycState === 0 ? '还没有实名' : '已经实名' }}</p>
        <template v-if="me.kycState === 1">
          <p class="body-2">姓名：...</p>
          <p class="body-2">身份证号：...</p>
        </template>
      </v-flex>
      <v-flex>
        <h4 class="mb-2 caption">我发布的需求</h4>
        <requirement-item
          v-for="req in requirements"
          v-bind:key="req.id"
          :requirement="req"
        ></requirement-item>
      </v-flex>
    </v-container>
  </loading>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import { getMe, getMyRequirements } from '@/services/api'
import { IUser, IRequirement } from '@/services/interface'
import RequirementItem from '@/components/RequirementItem.vue'

@Component({
  middleware: 'i18n',
  head () {
    return {
      title: this.title
    }
  },
  components: {
    RequirementItem
  }
})
class MePage extends Vue {
  requirements: Array<IRequirement> | [] = [];
  me: IUser = {};

  loading = false

  get title () {
    return '我'
  }

  mounted () {
    this.init()
  }

  async init () {
    this.loading = true
    await this.requesUser()
    this.loading = false
  }

  async requesUser () {
    try {
      const me = await getMe()
      const requirements = await getMyRequirements()
      this.me = me
      this.requirements = requirements
    } catch (error) {
      this.$errorHandler(this.$toast.bind(this), error)
    }
  }
}
export default MePage
</script>

<style lang="scss" scoped></style>
