<template lang="pug">
  div.basemargin
    el-row
      el-col(:span="2") 編集対象
      el-col(:span="22" v-text="user.name")
    el-transfer(v-model="roles" :data="roleList" :button-texts="['剥奪', '付与']" :titles="['未付与権限', '付与済み']" :props="{key: 'name', label: 'name'}")
    el-button(type="primary" @click="submitForm") 変更
    el-button(@click="cancel") キャンセル
</template>
<script>
import { makeArray, makeSingle } from '@/libraries/supports'
export default {
  props: ['id'],
  metaInfo() {
    return {
      title: this.title
    }
  },
  data() {
    return {
      title: 'ユーザー権限',
      user: {},
      roles: [],
      roleList: []
    }
  },
  methods: {
    getUser() {
      return this.$store.dispatch('maintenance/getUser', this.id).then((response) => {
        this.user = makeSingle(response.data, 'object')
      }).catch((error) => {
        this.$notify.error({ title: 'Error', message: error.message })
      })
    },
    getUserRoles() {
      return this.$store.dispatch('maintenance/getUserRoles', this.id).then((response) => {
        this.roles = makeArray(response.data, 'string')
      }).catch((error) => {
        this.$notify.error({ title: 'Error', message: error.message })
      })
    },
    getRoles() {
      this.roleList = []
      return this.$store.dispatch('maintenance/getRoleList').then((response) => {
        this.roleList = makeArray(response.data)
      }).catch((error) => {
        this.$notify.error({ title: 'Error', message: error.message })
      })
    },
    getUserEditData() {
      this.$store.dispatch('nowLoadingMainte', 'ユーザー情報処理中')
      this.getUser()
      this.getRoles().then(() => {
        this.getUserRoles().then(() => {
          this.$store.dispatch('endLoading')
        })
      })
    },
    submitForm() {
      this.$store.dispatch('nowLoadingMainte', 'ユーザー情報更新中')
      this.$store.dispatch('maintenance/setUserRoles', { id: this.id, roles: this.roles }).then((response) => {
        this.$notify({ title: '変更完了', message: 'ユーザー情報の更新を行いました' })
        this.$store.dispatch('endLoading')
        this.$router.go(-1)
      }).catch((error) => {
        this.$store.dispatch('endLoading')
        this.$notify.error({ title: 'Error', message: error.message })
      })
    },
    cancel() {
      this.$router.go(-1)
    }
  },
  created() {
    this.getUserEditData()
  },
  watch: {
    '$route': 'getUserEditData'
  },
  beforeRouteEnter(to, from, next) {
    next(vm => {
      vm.$store.commit('changeBreadcrumb',
        { path: vm.$route.path, name: vm.title }
      )
    })
  }
}
</script>
