<template>
  <div style="padding: 15px;">
    <el-card>
      <el-tabs stretch style="height: 100%" @tab-change="tap" v-model="state.currentTapName">
        <el-tab-pane :label="$t('message.adminServer.tapWebsite')" name="1">
          <el-row style="margin-bottom: 20px">
            <el-col :span="12">
              <div>
                <el-icon style="margin-right: 4px" :size="12"><InfoFilled /></el-icon>
                当前版本
              </div>
              <div style="font-size: 20px">{{serverConfig.version.value.currentVersion.version}}</div>
            </el-col>
            <el-col :span="12">
              <div><el-icon style="margin-right: 4px" :size="12"><UploadFilled /></el-icon>
                最新版本
              </div>
              <div style="color: red;font-size: 20px">{{serverConfig.version.value.latestVersion.version}}</div>
            </el-col>
          </el-row>

          <el-row>
            <el-button type="primary" :disabled="state.inShowUpdateButton" @click="openUpdateDialog">升级</el-button>
          </el-row>
          <el-divider></el-divider>

          <el-form :model="serverConfig.serverConfig.value.website" label-position="top">
            <el-form-item :label="$t('message.adminServer.Server.enable_register')" class="label">

              <el-switch v-model="serverConfig.serverConfig.value.website.enable_register" inline-prompt
                         :active-text="$t('message.common.enable')"
                         :inactive-text="$t('message.common.disable')"
                         style="--el-switch-on-color: #13ce66; --el-switch-off-color: #ff4949"></el-switch>


            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Server.acceptable_email_suffixes')" class="label">
              <el-input v-model="serverConfig.serverConfig.value.website.acceptable_email_suffixes" type="textarea"
                        autosize />
            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Server.enable_email_code')" class="label">
              <el-switch v-model="serverConfig.serverConfig.value.website.enable_email_code" inline-prompt
                         :active-text="$t('message.common.enable')"
                         :inactive-text="$t('message.common.disable')"
                         style="--el-switch-on-color: #13ce66; --el-switch-off-color: #ff4949"></el-switch>
            </el-form-item>
            <el-divider></el-divider>
            <el-form-item :label="$t('message.adminServer.Server.tek')" class="label">
              <el-input v-model="serverConfig.serverConfig.value.website.tek" />
            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Server.sub_name')" class="label">
              <el-input v-model="serverConfig.serverConfig.value.website.sub_name" />
            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Server.frontend_url')" class="label">
              <el-input v-model="serverConfig.serverConfig.value.website.frontend_url" placeholder="http://xxx.com" />
            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Server.backend_url')" class="label">
              <el-input v-model="serverConfig.serverConfig.value.website.backend_url" placeholder="http://xxx.com" />
            </el-form-item>
            <el-divider></el-divider>
            <el-form-item>
              <el-button @click="onSubmit()" type="primary">{{ $t("message.common.button_confirm") }}</el-button>
            </el-form-item>
          </el-form>
        </el-tab-pane>

        <el-tab-pane :label="$t('message.adminServer.tapPayment')" name="2">
          <div>
            <el-button size="default" type="primary" class="ml10" @click="openPayDialog('add')">
              <el-icon>
                <ele-FolderAdd />
              </el-icon>
              {{ $t("message.adminServer.addPay") }}
            </el-button>
          </div>
          <div>
            <el-table :data="adminShopStoreData.payList.value" stripe style="width: 100%;flex: 1;">
              <el-table-column type="index" :label="$t('message.adminShop.PayInfo.index')" fixed show-overflow-tooltip
                               width="60px" />
              <el-table-column prop="id" :label="$t('message.adminShop.PayInfo.id')" show-overflow-tooltip
                               width="60px" />
              <el-table-column prop="name" :label="$t('message.adminShop.PayInfo.name')" show-overflow-tooltip
                               width="200px" />
              <el-table-column prop="pay_type" :label="$t('message.adminShop.PayInfo.pay_type')" show-overflow-tooltip
                               width="120px" />
              <el-table-column prop="pay_logo_url" :label="$t('message.adminShop.PayInfo.pay_logo_url')"
                               show-overflow-tooltip width="120px">
                <template #default="{row}">
                  <el-image :src="row.pay_logo_url" style="width: 40px;height: 40px"></el-image>
                </template>
              </el-table-column>
              <el-table-column prop="status" :label="$t('message.adminShop.PayInfo.status')" show-overflow-tooltip
                               width="80px">
                <template #default="{row}">
                  <el-button v-if="row.status" type="warning">{{ $t("message.common.enable") }}</el-button>
                  <el-button v-else type="info">{{ $t("message.common.disable") }}</el-button>
                </template>
              </el-table-column>

              <el-table-column :label="$t('message.common.operate')">
                <template #default="scope">
                  <el-button text @click="openPayDialog('edit',scope.row)" type="primary">
                    {{ $t("message.common.modify") }}
                  </el-button>
                  <el-button text @click="deletePay(scope.row)" type="primary">{{ $t("message.common.delete") }}
                  </el-button>
                </template>
              </el-table-column>
            </el-table>
          </div>
        </el-tab-pane>

        <el-tab-pane :label="$t('message.adminServer.tapEmail')" name="3">
          <el-form :model="serverConfig.serverConfig.value.email" label-position="top">
            <el-form-item :label="$t('message.adminServer.Server.email_host')" class="label">
              <el-input v-model="serverConfig.serverConfig.value.email.email_host" placeholder="mail.example.com" />
            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Server.email_port')" class="label">
              <el-input v-model.number="serverConfig.serverConfig.value.email.email_port" type="number" />
            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Server.email_from')" class="label">
              <el-input v-model="serverConfig.serverConfig.value.email.email_from" placeholder="admin@qq.com" />
            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Server.email_from_alias')" class="label">
              <el-input v-model="serverConfig.serverConfig.value.email.email_from_alias" placeholder="admin@qq.com" />
            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Server.email_nickname')" class="label">
              <el-input v-model="serverConfig.serverConfig.value.email.email_nickname" placeholder="Admin" />
            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Server.email_secret')" class="label">
              <el-input v-model="serverConfig.serverConfig.value.email.email_secret" />
            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Server.email_subject')" class="label">
              <el-input v-model="serverConfig.serverConfig.value.email.email_subject" />
            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Server.email_content')" class="label">
              <el-input v-model="serverConfig.serverConfig.value.email.email_content" type="textarea" autosize />
              <el-text style="color: #9b9da1">{{ $t("message.adminServer.emailCodeTip") }}
              </el-text>
            </el-form-item>
            <el-divider></el-divider>
            <el-form-item>
              <el-button @click="onSubmit()" type="primary">{{ $t("message.common.button_confirm") }}</el-button>
              <el-button @click="onTestEmail">{{ $t("message.adminServer.emailTesting") }}</el-button>
            </el-form-item>
          </el-form>

        </el-tab-pane>

        <el-tab-pane :label="$t('message.adminServer.tapSecurity')" name="4">
          <el-form :model="serverConfig.serverConfig.value.security" label-position="top">
            <el-form-item :label="$t('message.adminServer.Server.ip_role_param')" class="label">
              <el-col :span="2">
                <el-input-number v-model="serverConfig.serverConfig.value.security.rate_limit_params.ip_role_param"
                                 :precision="0" :step="10" :min="0" :max="10000000" />
              </el-col>
              <el-col :span="2" style="text-align: center">
                <span>-</span>
              </el-col>
              <el-col :span="18">
                <span class="text-gray-500">{{ $t("message.adminServer.RequestMinute") }}</span>
              </el-col>
            </el-form-item>

            <el-form-item :label="$t('message.adminServer.Server.visit_param')" class="label">
              <el-col :span="2">
                <el-input-number v-model="serverConfig.serverConfig.value.security.rate_limit_params.visit_param"
                                 :precision="0" :step="10" :min="0" :max="10000000" />
              </el-col>
              <el-col :span="2" style="text-align: center">
                <span>-</span>
              </el-col>
              <el-col :span="18">
                <span class="text-gray-500">{{ $t("message.adminServer.RequestMinute") }}</span>
              </el-col>
            </el-form-item>
            <el-divider></el-divider>
            <el-form-item :label="$t('message.adminServer.Server.signing_key')" class="label">
              <el-input v-model="serverConfig.serverConfig.value.security.jwt.signing_key" />
            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Server.issuer')" class="label">
              <el-input v-model="serverConfig.serverConfig.value.security.jwt.issuer" />
            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Server.expires_time')" class="label">
              <el-input v-model="serverConfig.serverConfig.value.security.jwt.expires_time" />
            </el-form-item>
            <el-form-item>
              <el-button @click="onSubmit()" type="primary">{{ $t("message.common.button_confirm") }}</el-button>
            </el-form-item>
          </el-form>
        </el-tab-pane>

        <el-tab-pane :label="$t('message.adminServer.tapNotice')" name="5">
          <el-empty description="开发中，developing" />
          <el-form v-if="false" :model="serverConfig.serverConfig.value.notice" label-width="120px"
                   label-position="top">
            <el-form-item :label="$t('message.adminServer.Server.bot_token')" class="label">
              <el-input v-model="serverConfig.serverConfig.value.notice.bot_token"
                        placeholder="1234567890:AAAAABBBBBCCCCCDDDDFFFFGGGHHHJJKKLL" />
            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Server.tg_socks5')" class="label">
              <el-input v-model="serverConfig.serverConfig.value.notice.tg_socks5" placeholder="127.0.0.1:1080" />
            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Server.tg_admin')" class="label">
              <el-input v-model="serverConfig.serverConfig.value.notice.tg_admin" type="textarea" autosize />
            </el-form-item>

            <el-form-item :label="$t('message.adminServer.Server.when_node_offline')" class="label">
              <el-switch v-model="serverConfig.serverConfig.value.notice.when_node_offline" inline-prompt
                         :active-text="$t('message.common.enable')"
                         :inactive-text="$t('message.common.disable')"
                         style="--el-switch-on-color: #13ce66; --el-switch-off-color: #ff4949"></el-switch>
            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Server.when_user_registered')" class="label">
              <el-switch v-model="serverConfig.serverConfig.value.notice.when_user_registered" inline-prompt
                         :active-text="$t('message.common.enable')"
                         :inactive-text="$t('message.common.disable')"
                         style="--el-switch-on-color: #13ce66; --el-switch-off-color: #ff4949"></el-switch>
            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Server.when_user_purchased')" class="label">
              <el-switch v-model="serverConfig.serverConfig.value.notice.when_user_purchased" inline-prompt
                         :active-text="$t('message.common.enable')"
                         :inactive-text="$t('message.common.disable')"
                         style="--el-switch-on-color: #13ce66; --el-switch-off-color: #ff4949"></el-switch>
            </el-form-item>
            <el-form-item style="margin-top: 20px">
              <el-button @click="onSubmit()" type="primary">{{ $t("message.common.button_confirm") }}</el-button>
            </el-form-item>
          </el-form>
        </el-tab-pane>

        <el-tab-pane :label="$t('message.adminServer.tapMigration')" name="6">
          <div style="margin-bottom: 20px">
            <el-alert :title="$t('message.adminServer.migrationTip')" type="warning" effect="dark" />
          </div>

          <el-form v-model="state.migrationParams" label-position="top">
            <el-form-item :label="$t('message.adminServer.Migration.panel_type')" class="label">
              <el-select v-model="state.migrationParams.panel_type" placeholder="Select">
                <el-option
                  v-for="item in state.panels"
                  :key="item"
                  :label="item"
                  :value="item"
                />
              </el-select>
            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Migration.db_address')" class="label">
              <el-input v-model="state.migrationParams.db_address"></el-input>
            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Migration.db_port')" class="label">
              <el-input-number v-model="state.migrationParams.db_port"></el-input-number>
            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Migration.db_name')" class="label">
              <el-input v-model="state.migrationParams.db_name"></el-input>
            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Migration.db_username')" class="label">
              <el-input v-model="state.migrationParams.db_username"></el-input>
            </el-form-item>
            <el-form-item :label="$t('message.adminServer.Migration.db_password')" class="label">
              <el-input v-model="state.migrationParams.db_password"></el-input>
            </el-form-item>
            <el-form-item>
              <el-button color="blue" @click="onSubmitMigration">{{ $t("message.common.button_confirm") }}</el-button>
            </el-form-item>
          </el-form>

        </el-tab-pane>

      </el-tabs>
      <PayDialog ref="PayDialogRef" @refresh="adminShopStore.getPayList()"></PayDialog>
    </el-card>
    <el-dialog v-model="state.isShowTestEmailDialog" :title="$t('message.adminServer.emailTesting')" width="400px"
               destroy-on-close center>
      <el-form :model="state.emailParams" label-position="top">
        <el-form-item>
          <el-input v-model="state.emailParams.target_email" placeholder="xxx@xxx.com" />
        </el-form-item>
      </el-form>
      <template #footer>
      <span class="dialog-footer">
        <el-button @click="onGetEmailCode">{{ $t("message.common.button_confirm") }}</el-button>
      </span>
      </template>
    </el-dialog>
    <el-dialog v-model="state.isShowUpdateDialog" title="升级AirGo核心" width="80%"
               destroy-on-close center>
      <div v-if="serverConfig.version.value.currentVersion.version === serverConfig.version.value.latestVersion.version">
        <span>当前已是最新版本</span>
      </div>
      <div v-else>
        <el-row style="margin-bottom: 20px">
          <el-col :span="12" style="text-align: center;">
            <div >
              <el-icon style="margin-right: 4px" :size="12"><InfoFilled /></el-icon>
              <span>当前版本</span>
            </div>
            <div style="font-size: 20px">{{serverConfig.version.value.currentVersion.version}}</div>
          </el-col>
          <el-col :span="12" style="text-align: center;">
            <div><el-icon style="margin-right: 4px" :size="12"><UploadFilled /></el-icon>
              <span>最新版本</span>
            </div>
            <div style="color: red;font-size: 20px">{{serverConfig.version.value.latestVersion.version}}</div>
          </el-col>
        </el-row>
        <el-result icon="warning" title="升级有风险，请做好数据备份！"></el-result>
        <div v-if="state.isShowLogData">
          <el-alert style="margin: 10px 0 0" v-for="(v,k) in state.logData" :key="k" :title="v.title" :type="v.type" effect="dark" :closable="false"/>
        </div>

      </div>
      <template #footer>
      <span class="dialog-footer">
        <el-button @click="state.isShowUpdateDialog = false" >{{ $t("message.common.button_cancel") }}</el-button>
        <el-button type="primary" @click="SSE" :disabled="state.isShowLogData">开始升级</el-button>
      </span>
      </template>

    </el-dialog>
  </div>
</template>

<script lang="ts" setup>
import { defineAsyncComponent, onBeforeUnmount, onMounted, reactive, ref } from "vue";
import { useAdminServerStore } from "/@/stores/admin_logic/serverStore";
import { storeToRefs } from "pinia";
import { ElMessage, ElMessageBox } from "element-plus";
import { request,getApiPrefixAddress } from "/@/utils/request";
import { useApiStore } from "/@/stores/apiStore";
import { useUserStore } from "/@/stores/user_logic/userStore";
import { useAdminShopStore } from "/@/stores/admin_logic/shopStore";
import { usePublicStore } from "/@/stores/publicStore";
import { useI18n } from "vue-i18n";
import { useConstantStore } from "/@/stores/constantStore";

const apiStore = useApiStore();
const apiStoreData = storeToRefs(apiStore);
const PayDialog = defineAsyncComponent(() => import("/@/views/admin/system/dialog_pay.vue"));
const PayDialogRef = ref();
const serverStore = useAdminServerStore();
const serverConfig = storeToRefs(serverStore);
const adminShopStore = useAdminShopStore();
const adminShopStoreData = storeToRefs(adminShopStore);
const publicStore = usePublicStore();
const userStore = useUserStore();
const { t } = useI18n();
const constantStore = useConstantStore();


const state = reactive({
  currentTapName: "1",
  isShowTestEmailDialog: false,
  emailParams: {
    email_type: constantStore.EMAIL_TYPE_TEST,
    target_email: ""
  },
  loading: false,
  panels: ["v2board", "sspanel", "AirGo"],
  migrationParams: {
    "panel_type": "",
    "db_address": "",
    "db_port": 3306,
    "db_username": "",
    "db_password": "",
    "db_name": ""
  },
  migrationResult: "",
  inShowUpdateButton: false,
  isShowUpdateDialog: false,
  isShowLogData:false,
  logData:[{}],
});
const tap = (tapName: string) => {
  switch (tapName) {
    case "":
      break;
    default:
      break;
  }

};
//测试邮件
const onTestEmail = () => {
  state.isShowTestEmailDialog = true;
};
//获取邮件验证码
const onGetEmailCode = () => {
  if (state.emailParams.target_email === "") {
    return;
  }
  request(apiStoreData.publicApi.value.getEmailCode, state.emailParams).then((res) => {
    ElMessage.success(res.msg);
  });
};


//打开支付编辑
const openPayDialog = (type: string, row?: PayInfo) => {
  PayDialogRef.value.openDialog(type, row);
};

//保存提交
const onSubmit = () => {
  serverStore.updateServerConfig(serverConfig.serverConfig.value).then((res) => {
    ElMessage.success(res.msg);
    serverStore.getServerConfig();
    publicStore.getPublicSetting();
  });
};
const onSubmitMigration = () => {
  ElMessageBox.confirm(t("message.adminServer.migrationTip2"), t("message.common.tip"), {
    confirmButtonText: t("message.common.button_confirm"),
    cancelButtonText: t("message.common.button_cancel"),
    type: "warning"
  })
    .then(() => {
      state.loading = true;
      request(apiStore.adminApi.migrationData, state.migrationParams).then((res) => {
        state.migrationResult = res.data;
        state.loading = false;
      }).catch(() => {
        state.loading = false;
      });
    })
    .catch(() => {
    });
};
//删除支付
const deletePay = (data: PayInfo) => {
  ElMessageBox.confirm(t("message.common.message_confirm_delete"), t("message.common.tip"), {
    confirmButtonText: t("message.common.button_confirm"),
    cancelButtonText: t("message.common.button_cancel"),
    type: "warning"
  })
    .then(() => {
      request(apiStoreData.adminApi.value.deletePay, data).then((res) => {
        adminShopStore.getPayList(); //获取支付列表
      });
    })
    .catch(() => {
    });

};
//获取版本
const getVersion = () => {
  serverStore.getCurrentVersion();
  serverStore.getLatestVersion();
};
//打开升级弹窗
const openUpdateDialog=()=>{
  state.isShowUpdateDialog = true
}

const SSE = () => {
  state.isShowLogData = true
  state.logData=[]
  let url = getApiPrefixAddress()+apiStore.adminApi.updateLatestVersion.path
  if (window.EventSource) {
   let sseSource = new EventSource(url)
    sseSource.onopen = function (e:any) {
      console.log('建立连接', e)
    }
    sseSource.onmessage = function (e:any) {
      console.log("onmessage 收到数据",e.data,"消息类型",e.type)
      state.logData.push({"type":"info","title":e.data})
    }
    sseSource.onerror = function (e:any) {
      console.log('关闭连接', e)
      sseSource.close()
    }
    //自定义2个类型的监听，其中message error是为了和默认的 onerror 区别
    sseSource.addEventListener("success",function (e:any) {
      console.log("onmessage 收到数据",e.data,"消息类型",e.type)
      state.logData.push({"type":"success","title":e.data})
    })
    sseSource.addEventListener("message error",function (e:any) {
      console.log("onmessage 收到数据",e.data,"消息类型",e.type)
      state.logData.push({"type":"warning","title":e.data})
    })
  } else {
    console.log('浏览器不支持SSE')
  }
}

onMounted(() => {
  serverStore.getServerConfig(); //获取设置参数
  adminShopStore.getGoodsList(); //获取全部商品，用来设置新注册分配套餐
  adminShopStore.getPayList();   //获取支付列表
  getVersion();                  //获取版本
});

</script>

<style lang="scss">
.label .el-form-item__label {
  font-weight: bolder;
  //font-size: 15px;
}
</style>