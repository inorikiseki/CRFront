<template>
  <el-dropdown>
    <span>
      <el-tooltip
          class="box-item"
          effect="light"
          content="邀请通知"
          placement="left-start"
          :visible="tip_visible"
      >
        <el-icon
            class="effected-icon"
            color="#50b5ff" :size="25"
            @mouseenter="tip_visible = true"
            @mouseleave="tip_visible = false"
        >
          <Message/>
        </el-icon>
      </el-tooltip>
    </span>
    <template #dropdown>
      <el-scrollbar max-height="300px">

        <el-dropdown-menu style="min-width: 500px;">
          <el-dropdown-item
              v-for="notify in invitations">
            <el-row justify="space-evenly">
              <el-col :span="4">
                <mini-profile class="notify-profile"
                              :avatar_url="cr_default.avatar.purple"
                              :name="cr_default.user_info.name"
                              :user_id="cr_default.user_info.user_id"
                ></mini-profile>
              </el-col>
              <el-col :span="12" style="padding-top: 3em;">
                <el-text multiple>{{ notify.remark }}</el-text>
              </el-col>
              <el-col :span="4" style="align-self: center">
                <el-button type="primary">Yes</el-button>
              </el-col>
              <el-col :span="4" style="align-self: center">
                <el-button type="danger">No</el-button>
              </el-col>
            </el-row>
          </el-dropdown-item>
        </el-dropdown-menu>
      </el-scrollbar>
    </template>
  </el-dropdown>
</template>
<script>
import {Bell, Message} from "@element-plus/icons-vue";
import MiniProfile from "@/components/icons/MiniProfile.vue";
import cr_default from "@/store/modules/cr_default.js";

export default {
  computed: {
    cr_default() {
      return cr_default
    },
  },
  components: {Message, MiniProfile, Bell},
  data() {
    return {
      tip_visible: false,
      invitations: [
        {
          type: 'friend application',
          sender: {
            name: 'Bob',
            id: 'boblikeschatting',
          },
          time: '2024/7/11',
          remark: 'I want to make friends with you!'
        },
      ]
    }
  }
}
</script>

<style>

.notify-profile .user-info {
  flex-flow: row nowrap !important;
  align-self: start;
  margin-top: 3px;
}

</style>
