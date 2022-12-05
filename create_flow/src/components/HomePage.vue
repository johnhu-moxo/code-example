<template>
    <el-container>
    <el-button type="primary" @click="connectButtonClicked">Connect With New Client</el-button>
      <div class="choose-panel" v-if="templateListVisible">
        <div class="choose-panel-title">
          <span>Choose Project</span>
          <el-button type="text" @click="handleClose">Close</el-button>
        </div>
        <div
            v-loading="templateloading"
            id="templatelist"
            class="panel-container"
            style="height:100%;width:100%"></div>
      </div>
    </el-container>
    <el-container>
        <el-main>
          <div class="chatbox" id="chatbox"></div>
        </el-main>
    </el-container>
</template>

<script>
export default ({
  props: {
    templateWindow: null
  },
  data() {
    return {
      api_domain: '',
      sdk_domain:'',
      unique_id: '',
      org_id: '',
      client_id: '',
      client_secret: '',
      templateListVisible: false,
      templateloading: false
    }
  },
  methods: {
    connectButtonClicked() {
        this.axios.post(this.api_domain, {
          'client_id': this.client_id,
          'unique_id': this.unique_id,
          'org_id': this.org_id,
          'client_secret': this.client_secret
        }).then(res => {
          this.token = res.data.access_token;
          //Go initilize Moxo SDK
          let flowsdk = window.flowsdk;
          flowsdk.setupDomain(this.sdk_domain)
          this.templateListVisible = true
          this.templateloading = true
          flowsdk.linkWithAccessToken(this.token).then(() => {
            this.templateloading = false
            flowsdk.showTemplateList('#templatelist')
            flowsdk.onNewFlowCreated((board) => {
              this.templateListVisible = false
              //Open chat
              this.flowId = board.board.id;
              flowsdk.openFlow('#chatbox', board.board.id)
            })
          }).catch((error => {
            console.log(`login failed ${error}`)
          }))
          flowsdk.onFlowUpdated((board) => {
            if (board.flow_id && board.status) {
              this.$notify({
                title: 'Your flow updated!',
                message: `Flow ${board.flow_id} status updated to ${board.status}`
              });
            }
          })
          console.log(res);
        }).catch(res => {
          console.log(res);
        })
    },
    handleClose() {
        this.templateListVisible = false
    }
  }
})
</script>

<style>
.chatbox {
  height: 600px
}
.choose-panel {
  position: fixed;
  top: 0;
  right: 0;
  background: white;
  bottom: 0;
  left: 0;
  z-index: 99;
  display: flex;
  flex-direction: column;
}
.choose-panel-title {
    display: flex;
    justify-content: space-between;
    height: 40px;
    line-height: 40px;
    align-items: center;
    padding: 16px;
}
.panel-container {
    flex: 1 1 auto;
}
</style>