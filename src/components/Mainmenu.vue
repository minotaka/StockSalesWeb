<template lang="pug">
  div
    el-row
      el-col(:xs="12" :sm="8" :md="6" :lg="4")
        el-menu.rightSpace(mode="vertical" theme="dark")
          el-menu-item(index="1" @click="goSelect") 在庫・販売状況確認
          el-menu-item(index="2" @click="goPlanUp") 予算データアップロード
          // el-menu-item(index="3") パスワード変更
          // el-menu-item(index="4") メールアドレス変更
          el-menu-item(index="5" @click="goMainte") 管理機能
          // el-menu-item(index="6") サインインログ表示
          // el-menu-item(index="6") アクションログ表示
      el-col(:xs="12" :sm="16" :md="18" :lg="20" v-loading="loading" element-loading-text="読み込み中")
        el-table(:data="dashboards" v-loading="loading" element-loading-text="Loading...")
          el-table-column(prop="startDateTime" label="掲載日" width="180")
            template(scope="scope")
              span {{scope.row.startDateTime | converetDateFormat}}
          el-table-column(prop="message" label="メッセージ")
</template>
<script>
import { makeArray } from '@/libraries/supports'
export default {
  metaInfo() {
    return {
      title: this.title
    }
  },
  data() {
    return {
      title: 'メニュー',
      loading: false,
      dashboards: []
    }
  },
  methods: {
    goSelect() {
      this.$router.push('/makerselect')
    },
    goPlanUp() {
      this.$router.push('/planupload')
    },
    goMainte() {
      this.$router.push('/mainte')
    },
    getDashboard() {
      this.loading = true
      this.$store.dispatch('getDashboard').then((response) => {
        var items = response.data
        this.dashboards = makeArray(items)
        this.loading = false
      }).catch((error) => {
        this.$notify({ title: 'Warning', message: error.message, type: 'warning' })
        this.loading = false
      })
    }
  },
  created() {
    this.$store.commit('setBreadcrumb',
      { path: this.$route.path, name: this.title }
    )
    this.getDashboard()
  },
  watch: {
    '$route': 'getDashboard'
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
<style lang="scss" scoped>
.rightSpace {
  margin-right: 8px;
}
</style>
