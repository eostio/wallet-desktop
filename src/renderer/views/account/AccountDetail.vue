<template>
  <div class="dashboard-body">
    <account-overview />
    <div class="tabs">
      <div class="tab fl" @click="toggleTab('BpList')" :class="{'is-active': currentTab === 'BpList'}">
        <a class="min-img">
          <img src="@/assets/node.png" class="two hide">
          <img src="@/assets/node_w.png" class="one">
          节点列表
        </a>
      </div>
       <div class="tab fl" @click="toggleTab('TransferRecord')" :class="{'is-active': currentTab === 'TransferRecord'}">
        <a class="min-img">
          <img src="@/assets/exchange.png" class="two hide">
          <img src="@/assets/exchange_w.png" class="one">
          交易记录
        </a>
      </div>
    </div>
    <div :is="currentTab" keep-alive></div>
    <router-view name="modal"></router-view>
  </div>
</template>

<script>
import { mapState, mapActions } from 'vuex'

import Message from '@/components/Message'
import { Actions, Getters } from '@/constants/types.constants'
import AccountOverview from '@/views/account/AccountOverview'
import TransferRecord from '@/views/account/TransferRecord'
import BpList from '@/views/account/BpList'

export default {
  name: 'AccountDetail',
  data() {
    return {
      currentTab: 'BpList', // currentTab 用于标识当前触发的子组件,
    }
  },
  computed: {
    accountName() {
      return this.$route.params.accountName
    },
    walletData() {
      return this.wallet.data || {}
    },
    ...mapState(['wallet']),
  },
  methods: {
    initAccount() {
      return this.initApp().then(() => {
        this.fetchAccout({ accountName: this.accountName })
      })
    },
    ...mapActions({
      fetchAccout: Actions.FETCH_ACCOUNT,
      fetchWallet: Actions.FETCH_WALLET,
      initApp: Actions.INIT_APP,
    }),
    toggleTab: function(tab) {
      this.currentTab = tab // tab 为当前触发标签页的组件名
    },
  },
  beforeRouteUpdate(to, from, next) {
    if (to.params.accountName !== from.params.accountName) {
      this.initAccount()
    }
    next()
  },
  created() {
    if (this.walletData.publicKey) {
      return this.initAccount()
    } else {
      this.fetchWallet({ id: this.$route.params.walletId }).then(() => {
        return this.initAccount()
      })
    }
  },
  components: {
    AccountOverview,
    TransferRecord,
    BpList,
  },
}
</script>

<style scoped>
.dashboard-body {
  padding: 24px;
  overflow: auto;
  background: #1e2333;
  flex: 1;
}
</style>

