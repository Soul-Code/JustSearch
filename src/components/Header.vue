 <template>
  <div>

    <div class="mu-appbar-fix">
      <mu-appbar v-if="!isPc" z-depth='0' color="grey800">
        <mu-button v-if="!isPc" @click="open = !open" icon slot="left">
          <mu-icon value="menu"></mu-icon>
        </mu-button>
        <router-link to="/justsoso" slot="left" class="title">
          Just搜搜 | 网络答题系统
        </router-link>
        <mu-button v-if="!isLogin" to="/justsoso/Login" flat class="btn" slot="right">
          登录
        </mu-button>
        <mu-button v-else @click="logout" flat class="btn" slot="right">
          退出登录
        </mu-button>
      </mu-appbar>

      <mu-appbar v-if="isPc" color="grey800">
        <mu-container>
          <mu-appbar z-depth='0' color="grey800">
            <mu-button v-if="!isPc" icon slot="left">
              <mu-icon value="menu"></mu-icon>
            </mu-button>
            <router-link to="/justsoso" slot="left" class="title">
              Just搜搜 | 网络答题系统 PC端
            </router-link>
            <mu-tabs :value.sync="active" color='grey800' indicator-color='primary' full-width>
              <mu-tab to="/justsoso/Answer" class="tabs">答题</mu-tab>
              <mu-tab to="/justsoso/Rank" class="tabs">排名</mu-tab>
              <mu-tab to="/justsoso/myTeam" class="tabs">我的队伍</mu-tab>
              <mu-tab to="/justsoso/Login" class="tabs">登录</mu-tab>
            </mu-tabs>
            <!-- <mu-button round class="btn" color='primary' slot="right">
            上传答案
          </mu-button> -->
            <mu-button v-if="isLogin" @click="logout" round class="btn" color="grey600" slot="right">
              退出登录
            </mu-button>
            <!-- <mu-button v-else to="/justsoso/Login" round class="btn" color="grey600" slot="right">
            登录
          </mu-button> -->

          </mu-appbar>
        </mu-container>
      </mu-appbar>
    </div>
    <mu-drawer :open.sync="open" :docked="docked" :right="position === 'right'">
      <mu-list>
        <mu-list-item button @click="open = false" to="/justsoso">
          <mu-list-item-title>比赛须知</mu-list-item-title>
        </mu-list-item>
        <mu-list-item button @click="open = false" to="/justsoso/Answer">
          <mu-list-item-title>答题</mu-list-item-title>
        </mu-list-item>
        <mu-list-item button @click="open = false" to="/justsoso/Rank">
          <mu-list-item-title>排名</mu-list-item-title>
        </mu-list-item>
        <mu-list-item button @click="open = false" to="/justsoso/myTeam">
          <mu-list-item-title>我的队伍</mu-list-item-title>
        </mu-list-item>
        <!-- <mu-list-item v-if="!isLogin" button @click="open = false" to="/justsoso/Login">
          <mu-list-item-title>登录</mu-list-item-title>
        </mu-list-item>
        <mu-list-item v-else button @click="open = false;logout" >
          <mu-list-item-title >退出登录</mu-list-item-title>
        </mu-list-item> -->
      </mu-list>
    </mu-drawer>
    <mu-dialog v-if="isLogined && msg" :title="message[0]" width="600" max-width="80%" :open.sync="openAlert">
      {{ message[1] }}
      <mu-button slot="actions" flat color="primary" @click="openAlert=false">我知道了</mu-button>
    </mu-dialog>
  </div>
</template>
<script>
export default {
  props: {
    isLogined: Boolean
  },
  data() {
    return {
      //Drawer
      docked: false,
      open: false,
      position: "left",

      isLogin: false,
      active: 0,
      fullWidth: document.documentElement.clientWidth,
      isPc: document.documentElement.clientWidth > 1000,
      openAlert: true,
      msg,
      msgText: localStorage.getItem("msg")
    };
  },
  watch: {
    active() {
      this.isLogin = localStorage.getItem("isLogin");
    },
    isLogined() {
      console.log("登录了");
      this.isLogin = localStorage.getItem("isLogin");
    }
  },
  mounted() {
    const that = this;
    this.active = this.$route.name;
    this.isLogin = localStorage.getItem("isLogin");
    window.onresize = () => {
      return (() => {
        window.fullHeight = document.documentElement.clientHeight;
        that.fullHeight = window.fullHeight;
        that.isPc = that.fullWidth > 1000;
      })();
    };
  },
  computed: {
    message() {
      return localStorage.getItem("msg").split("/");
    }
  },
  methods: {
    logout() {
      this.$axios
        .get(this.url + "logout")
        .then(res => {
          if (res.data.isOk) {
            localStorage.setItem("isLogin", "");
            this.isLogin = localStorage.getItem("isLogin");
            this.$toast.success("退出登录成功！");
            this.$router.push("Login");
          } else {
            console.log("登出错误");
            this.show_toast(res.data.errmsg, 1);
          }
        })
        .catch(res => {
          console.log(res);
          this.show_toast("服务器连接失败！", 1);
        });
    },
    show_toast(string, type) {
      // console.log(string)
      if (type == 1) {
        this.$toast.error(string);
      } else {
        this.$toast.success(string);
      }
    }
  }
};
</script>
<style>
.btn {
  margin-left: 6px;
  margin-right: 6px;
  font-size: 1rem;
}
.title {
  font-size: 1.1rem;
  color: aliceblue;
}
.tabs {
  font-size: 1rem;
}
.mu-appbar-fix {
  position: fixed;
  top: 0;
  width: 100%;
  z-index: 999;
}
</style>

