<template>
  <mu-flex justify-content="center">
    <mu-card raised class="card-main">
      <mu-flex wrap="wrap" v-bind:justify-content="isPc?'between':'center'" v-if="userInfo">
        <mu-flex fill>
          <!-- 个人信息卡 -->
          <mu-card v-loading="loading1" class="card-info" style="">
            <mu-card-title class="title-name" :title="userInfo.name">
            </mu-card-title>
            <mu-list dense>
              <!-- <mu-list-item>
                个人积分：{{userInfo.score}}分
              </mu-list-item> -->
              <mu-list-item v-if="userInfo.qq">
                QQ：{{userInfo.qq}}
              </mu-list-item>
              <mu-list-item v-if="userInfo.tel">
                TEL：{{userInfo.tel}}
              </mu-list-item>
            </mu-list>
            <mu-list v-if="userInfo.team.id">

              <mu-sub-header>我的队伍</mu-sub-header>
              <mu-list-item avatar button :ripple="true">
                <mu-list-item-content>
                  <mu-list-item-title>{{userInfo.team.name}}</mu-list-item-title>
                  <mu-list-item-sub-title><span style="margin-right:10px"> ID：{{userInfo.team.id}}</span></mu-list-item-sub-title>
                    <!-- 积分：{{userInfo.team.score}}分 -->
                </mu-list-item-content>

                <mu-list-item-action v-if="userInfo.team.score==0">

                  <mu-tooltip v-if="userInfo.team.leader != userInfo.name" placement="top" content="退出队伍">
                    <mu-button icon color="blue" @click="quitTeam(userInfo.id,false)">
                      <mu-icon value="reply"></mu-icon>
                    </mu-button>
                  </mu-tooltip>

                  <mu-tooltip v-else placement="top" content="解散队伍">
                    <mu-button icon color="red" @click="openDel=true">
                      <mu-icon value="delete"></mu-icon>
                    </mu-button>
                  </mu-tooltip>

                  <mu-dialog title="解散队伍" width="400px" max-width="80%" :esc-press-close="false" :overlay-close="false"
                    :open.sync="openDel">
                    确认解散队伍？你的队员将无家（队）可归！
                    <mu-button slot="actions" @click="openDel = false">我再想想</mu-button>
                    <mu-button slot="actions" v-loading="loading2" data-mu-loading-size="24" :disabled="loading2" color="red"
                      @click="delTeam">我意已决 </mu-button>
                  </mu-dialog>

                </mu-list-item-action>
              </mu-list-item>

              <mu-list-item avatar :ripple="true" button>
                <mu-list-item-action class="avatar-member">
                  <mu-icon value="person"></mu-icon>
                </mu-list-item-action>
                <mu-list-item-content>
                  <mu-list-item-title>{{userInfo.team.leader}}</mu-list-item-title>
                </mu-list-item-content>
                <mu-list-item-action>
                  <mu-badge content="leader" color="secondary"></mu-badge>
                </mu-list-item-action>
              </mu-list-item>

              <mu-list-item v-for="mem in userInfo.team.mems" v-if="mem.name!=userInfo.team.leader" :key="mem.id"
                avatar :ripple="true" button>
                <mu-list-item-action class="avatar-member">
                  <mu-icon value="person"></mu-icon>
                </mu-list-item-action>
                <mu-list-item-content>
                  <mu-list-item-title>{{mem.name}}</mu-list-item-title>
                </mu-list-item-content>
                <mu-list-item-action v-if="userInfo.team.leader == userInfo.name && mem.name !=userInfo.name && userInfo.team.score==0">
                  <mu-tooltip placement="top" content="踢出队员">
                    <mu-button @click="quitTeam(mem.id,true)" icon color="error">
                      <mu-icon value="close"></mu-icon>
                    </mu-button>
                  </mu-tooltip>
                </mu-list-item-action>
              </mu-list-item>

            </mu-list>
          </mu-card>
        </mu-flex>
        <mu-flex fill>
          <div class="demo-vsteper-container">
            <mu-stepper :active-step="vactiveStep" orientation="vertical">
              <!-- step1 报名 -->
              <mu-step>
                <mu-step-label>
                  报名比赛
                </mu-step-label>
                <mu-step-content>
                  <p>
                    <span v-if="!userInfo.tel">登录成功，现在点击下面的按钮输入信息来报名~</span>
                    <span v-else>报名成功，您现在可以点击下面的按钮修改信息~</span>
                  </p>
                  <mu-button class="demo-step-button" @click="openRegister = true;
                          validateForm.tel=userInfo.tel;
                          validateForm.qq=userInfo.qq"
                    color="primary">
                    <span v-if="!userInfo.tel">点我报名</span>
                    <span v-else>修改信息</span>
                  </mu-button>
                  <mu-button v-if="userInfo.tel" flat class="demo-step-button" @click="vhandleNext">下一步</mu-button>

                  <!-- <mu-dialog :title="userInfo.tel?'修改信息':'请完善信息进行报名'" width="400px" max-width="80%" :esc-press-close="false"
                    :overlay-close="false" :open.sync="openRegister"> -->
                     <mu-dialog :title="userInfo.tel?'禁止修改信息':'报名已截止'" width="400px" max-width="80%" :esc-press-close="false"
                    :overlay-close="false" :open.sync="openRegister">
                    <!-- <mu-form ref="form" :model="validateForm" class="mu-demo-form">
                      <mu-form-item label-float label="手机号" help-text="请保证手机号码通信畅通！" prop="tel" :rules="telRules">
                        <mu-text-field type="number" v-model="validateForm.tel" prop="tel"></mu-text-field>
                      </mu-form-item>
                      <mu-form-item class="lastone" label-float label="QQ" prop="qq" :rules="qqRules">
                        <mu-text-field type="number" v-model="validateForm.qq" prop="qq"></mu-text-field>
                      </mu-form-item>
                    </mu-form> -->
                    <p>
                      <span v-if="!userInfo.tel">你来晚了呀，已经不可以报名了哟</span>
                      <span v-else>已经不可以修改信息了哟</span>
                    </p>
                    <mu-button slot="actions" @click="openRegister = false">返回</mu-button>
                    <!-- <mu-button v-loading="loading4" data-mu-loading-size="24" :disabled="loading4" slot="actions" color="success" @click="submitEnroll">
                      <span v-if="!userInfo.tel">确认报名</span>
                      <span v-else>确认修改</span>
                    </mu-button> -->
                  </mu-dialog>

                </mu-step-content>
              </mu-step>
              <!-- step2 组队 -->
              <mu-step v-if="!userInfo.team.id">
                <mu-step-label>
                  加入/创建队伍
                </mu-step-label>
                <mu-step-content>
                  <p>
                    参加比赛的小可爱们，恭喜你们报名成功啦。现在的你们可以选择<strong>创建</strong>属于自己的队伍（你将成为队长），或者选择<strong>加入</strong>别人的队伍~<br>
                    Ps.每个队伍为3人哟~
                  </p>
                  <mu-button class="demo-step-button" @click="openJoinTeam = true" color="primary">加入队伍</mu-button>
                  <mu-button class="demo-step-button" @click="openNewTeam = true;" color="primary">创建队伍</mu-button>

                  <mu-dialog title="创建新的队伍" width="400px" max-width="80%" :esc-press-close="false" :overlay-close="false"
                    :open.sync="openNewTeam">
                    <mu-form ref="form" :model="newTeamForm" class="mu-demo-form">
                      <mu-form-item label-float label="队伍名称" help-text="创建队伍你将成为队长！" prop="teamname" :rules="teamnameRules">
                        <mu-text-field type="text" v-model="newTeamForm.teamname" prop="teamname"></mu-text-field>
                      </mu-form-item>
                    </mu-form>
                    <mu-button slot="actions" @click="openNewTeam = false">返回</mu-button>
                    <mu-button v-loading="loading5" data-mu-loading-size="24" :disabled="loading5" slot="actions" color="success"
                      @click="submitCreate">确认创建</mu-button>
                  </mu-dialog>

                  <mu-dialog title="加入队伍" width="400px" max-width="80%" :esc-press-close="false" :overlay-close="false"
                    :open.sync="openJoinTeam">
                    <mu-form ref="form" :model="teamFindForm" class="mu-demo-form">
                      <mu-form-item label-float label="队伍id/名称/队长" help-text="优先匹配id" prop="teamfind" :rules="teamfindRules">
                        <mu-text-field type="text" v-model="teamFindForm.teamfind" prop="teamfind"></mu-text-field>
                      </mu-form-item>
                      <span v-if="teamFound.name" style="color:#000;padding-right:10px"> <strong>{{teamFound.name}}</strong>
                      </span> <span v-if="teamFound.id"> id:{{teamFound.id}} 队长：{{teamFound.leader}}</span>
                    </mu-form>
                    <mu-button slot="actions" @click="openJoinTeam = false">返回</mu-button>
                    <mu-button v-loading="loading5" data-mu-loading-size="24" :disabled="loading5 && !teamFound.id"
                      slot="actions" color="success" @click="submitJoin">加入队伍</mu-button>
                  </mu-dialog>

                  <mu-button flat class="demo-step-button" @click="vhandlePrev" disabled>上一步</mu-button>
                </mu-step-content>
              </mu-step>
              <!-- step2 分享队伍 -->
              <mu-step v-if="userInfo.team.id && userInfo.team.mems.length<=3">
                <mu-step-label>
                  分享队伍
                </mu-step-label>
                <mu-step-content>
                  <p>
                    恭喜你已经拥有了自己的队伍哦，但是由于你的队伍成员不满三人，暂时无法参赛呢，你可以选择分享队伍，让更多的小伙伴加入哦~
                    <br>
                    <span style="color:red">
                      <strong> 注意！请慎重选择你的队友，一旦开始答题，队友将不可更换。
                        现场赛时会验证身份，一旦发现代替他人参赛者，主办方有权取消其比赛资格！有队员来不了的同学会吃亏的哟~
                      </strong>
                    </span>
                  </p>
                  <mu-button class="demo-step-button" @click="openShare=true;" color="primary">分享队伍</mu-button>

                  <mu-dialog title="分享队伍" width="360" :open.sync="openShare">
                    队伍的ID：{{userInfo.team.id}}<br>
                    队伍名称：{{userInfo.team.name}}<br>
                    队长姓名：{{userInfo.team.leader}}<br>
                    <!-- <mu-button @click="qrcode" color="primary">加载二维码</mu-button>
                    <div id="qrcode" >
                    </div> -->
                    <mu-button slot="actions" flat color="primary" @click="openShare=false">关闭</mu-button>
                  </mu-dialog>

                  <mu-button flat class="demo-step-button" @click="vhandlePrev" disabled>上一步</mu-button>
                </mu-step-content>
              </mu-step>
              <!-- step3 准备参赛 -->
              <mu-step>
                <mu-step-label>
                  准备参赛
                </mu-step-label>
                <mu-step-content>
                  恭喜你找到了自己合适的队友，现在可以在左侧对队伍进行调整，一旦开始答题，小组成员将<strong style="color:red">不可变更</strong> ！
                </mu-step-content>
              </mu-step>

              <mu-step>
                <mu-step-label>
                  预选赛
                </mu-step-label>
                <mu-step-content>
                  <p>
                    1.以团队线上答题的形式，在预选赛期间在网站上答题，共179道，每道题只有两次答题机会，每小题分值相同，按照答题得分进行排名。<br>
                    2.前50组最终进入决赛。
                  </p>
                  <mu-button class="demo-step-button" @click="goAnswer" color="primary" :disabled="userInfo.isFinished">{{!userInfo.isFinished?'参加比赛':'已经完成'}}</mu-button>
                </mu-step-content>
              </mu-step>
              <mu-step>
                <mu-step-label>
                  团队赛
                </mu-step-label>
                <mu-step-content>
                  <p>
                    1.本环节为未进入决赛的团体中成员以个人身份参加，角逐个人赛的参赛名额。 <br>
                    2.本环节为个人答题，每人共10道题，每道题有两次提交机会，从复活赛开始计时，提交错误罚时2分钟，限时20分钟。<br>
                    3.复活赛复活排名前15名选手。
                  </p>
                  <mu-button class="demo-step-button" @click="goAnswer" color="primary">参加比赛</mu-button>
                </mu-step-content>
              </mu-step>

              <mu-step>
                <mu-step-label>
                  复活赛
                </mu-step-label>
                <mu-step-content>
                  <p>
                    1.本环节为未进入决赛的团体中成员以个人身份参加，角逐个人赛的参赛名额。<br />
                    2.本环节为个人答题，每人共10道题，每道题有两次提交机会，从复活赛开始计时，提交错误罚时2分钟，限时20分钟。<br />
                    3.复活赛复活排名前15名选手。<br>
                  </p>
                  <mu-button class="demo-step-button" @click="goAnswer" color="primary">参加比赛</mu-button>
                </mu-step-content>
              </mu-step>

              <mu-step>
                <mu-step-label>
                  个人赛
                </mu-step-label>
                <mu-step-content>
                  <p>
                    1.个人赛是由团队赛晋级的75人加复活成功的15人组成。个人赛时长为1个小时，共30题，每道题有且只有两次答题机会，提交错误会有罚时，罚时依照y=2^n，【简单：中等：困难 n=0：1：2】。<br>
                    2.个人赛过程中，选手可以自主选择题目难度，最终以答题得分和时间得分计算总分数和排名。
                  </p>
                  <mu-button class="demo-step-button" @click="goAnswer" color="primary">参加比赛</mu-button>
                </mu-step-content>
              </mu-step>

            </mu-stepper>
          </div>
        </mu-flex>
      </mu-flex>
      <mu-flex v-else>
        <h1>获取个人信息失败</h1>
      </mu-flex>
    </mu-card>
  </mu-flex>
</template>
<script>
  // import QRCode from "qrcodejs2";
  var debounce = require("lodash.debounce");
  export default {
    data() {
      return {
        vactiveStep: 1,
        isTeamed: true,
        fullWidth: document.documentElement.clientWidth,
        isPc: document.documentElement.clientWidth > 900,
        userInfo,
        openRegister: false,
        openNewTeam: false,
        openJoinTeam: false,
        openShare: false,
        openDel: false,
        teamFound: {},

        loading1: true,
        loading2: false,
        loading3: false,
        loading4: false,
        loading5: false,

        telRules: [{
            validate: val => !!val,
            message: "请填写手机号码"
          },
          {
            validate: val => /^(13[0-9]|14[579]|15[0-3,5-9]|16[6]|17[0135678]|18[0-9]|19[89])\d{8}$/.test(val),
            message: "手机号码有误，请确认输入是否正确"
          }
        ],
        qqRules: [{
            validate: val => !!val,
            message: "请填写qq号码"
          },
          {
            validate: val => val.length >= 6 && val.length <= 12,
            message: "qq号码长度不正确"
          }
        ],
        teamnameRules: [{
            validate: val => !!val,
            message: "请填写队伍名称"
          },
          {
            validate: val => val.length >= 3 && val.length <= 10,
            message: "队伍名称长度：3-10"
          },
          {
            validate: val => !/[`~@#$%^&*()+<>:"{},.\/;'·！#￥（——）：；“”‘、，|《。》、【】[\]]/.test(val),
            message: "队伍名称不能出现特殊字符"
          }
        ],
        teamfindRules: [{
            validate: val => !!val,
            message: "请填写查找队伍信息！"
          },
          {
            validate: val => !/[`~@#$%^&*()+<>:"{},.\/;'·！#￥（——）：；“”‘、，|《。》、【】[\]]/.test(val),
            message: "输入有误，请重新输入"
          }
        ],
        validateForm: {
          tel: "",
          qq: ""
        },
        newTeamForm: {
          teamname: ""
        },
        teamFindForm: {
          teamfind: ""
        }
      };
    },
    computed: {
      vfinished() {
        return this.vactiveStep > 4;
      }
    },
    watch: {
      "teamFindForm.teamfind": function (newTeam, oldTeam) {
        if (newTeam.length == 0) {
          this.teamFound = {};
          return;
        }
        this.debouncedGetTeam();
      }
    },
    methods: {
      goAnswer() {
        this.$router.push("Answer");
      },
      vhandleNext() {
        this.vactiveStep++;
      },
      vhandlePrev() {
        this.vactiveStep--;
      },
      vreset() {
        this.vactiveStep = 0;
      },
      submitEnroll() {
        //报名比赛
        this.$refs.form.validate().then(result => {
          // console.log("form valid: ", result);
          if (result) {
            this.loading4 = true;
            this.$axios
              .post(this.url + "register", this.validateForm)
              .then(res => {
                // console.log(res);
                if (res.data.isOk) {
                  this.openRegister = false;
                  this.vhandleNext();
                  this.show_toast("报名成功！", 0);
                  this.userInfo = res.data.userInfo;
                  this.loading4 = false;
                  refreshStep();
                } else {
                  this.show_toast(res.data.errmsg, 1);
                  this.loading4 = false;
                }
              })
              .catch(res => {
                // console.log(res);
                this.show_toast("服务器连接失败，请稍后重试。", 1);
                this.loading4 = false;
              });
          }
        });
      },
      submitCreate() {
        //创建队伍
        this.$refs.form.validate().then(result => {
          if (result) {
            this.loading5 = true;
            this.$axios
              .post(this.url + "new_team", this.newTeamForm)
              .then(res => {
                // console.log(res);
                if (res.data.isOk) {
                  this.openNewTeam = false;
                  // this.vhandleNext();
                  this.show_toast("队伍创建成功！", 0);
                  this.userInfo = res.data.userInfo;
                } else {
                  this.show_toast(res.data.errmsg, 1);
                }
                this.refreshStep();
                this.loading5 = false;
              })
              .catch(res => {
                // console.log(res);
                this.show_toast("服务器连接失败，请稍后重试。", 1);
                this.loading5 = false;
              });

            console.log(this.newTeamForm);
          }
        });
      },
      submitJoin() {
        //加入队伍
        if (!this.teamFound.id) {
          return;
        }
        this.loading5 = true;
        this.$axios
          .post(this.url + "join_team", this.teamFound)
          .then(res => {
            console.log(res);
            if (res.data.isOk) {
              this.openJoinTeam = false;
              // this.vhandleNext();
              this.show_toast("加入队伍成功！", 0);
              this.userInfo = res.data.userInfo;
            } else {
              this.show_toast(res.data.errmsg, 1);
            }
            this.refreshStep();
            this.loading5 = false;
          })
          .catch(res => {
            console.log(res);
            this.show_toast("服务器连接失败，请稍后重试。", 1);
            this.loading5 = false;
          });
      },

      shareTeam() {
        //可能会展示二维码
      },
      getTeam() {
        this.teamFound = {
          name: "正在查询……"
        };
        var that = this;
        if (this.teamFindForm.teamfind.length != 0) {
          this.$axios
            .post(this.url + "find_team", this.teamFindForm)
            .then(res => {
              console.log(res);
              if (res.data.isOk) {
                this.teamFound = res.data.team;
              } else {
                this.teamFound = {
                  name: "没有找到对应的队伍……"
                };
              }
            })
            .catch();
        } else {
          this.teamFound = {};
        }
      },
      quitTeam(memid, isLeader) {
        this.$axios

          .post(this.url + "quit_team", {
            id: memid
          })
          .then(res => {
            console.log(res);
            if (!isLeader) {
              if (res.data.isOk) {
                this.show_toast("退出队伍成功！", 0);
                this.userInfo = res.data.userInfo;
              } else {
                this.show_toast(res.data.errmsg, 1);
              }
            } else {
              if (res.data.isOk) {
                this.show_toast("删除队员成功！", 0);
                this.userInfo = res.data.userInfo;
              } else {
                this.show_toast(res.data.errmsg, 1);
              }
            }
            this.refreshStep();
          })
          .catch(res => {
            console.log(res);
            this.show_toast("服务器连接失败，请稍后重试。", 1);
          });
      },
      delTeam() {
        this.loading2 = true;
        this.$axios
          .post(this.url + "del_team")
          .then(res => {
            console.log(res);
            if (res.data.isOk) {
              this.show_toast("解散队伍成功！", 0);
              this.userInfo = res.data.userInfo;
              this.openDel = false;
            } else {
              this.show_toast(res.data.errmsg, 1);
            }
            this.loading2 = false;
            this.refreshStep();
          })
          .catch(res => {
            console.log(res);
            this.show_toast("服务器连接失败，请稍后重试。", 1);
            this.loading2 = false;
          });
      },

      show_toast(string, type) {
        // console.log(string)
        if (type == 1) {
          this.$toast.error(string);
        } else {
          this.$toast.success(string);
        }
      },
      qrcode() {
        let qrcode = new QRCode("qrcode", {
          width: 200,
          height: 200, // 高度
          text: this.url // 二维码内容
          // render: 'canvas'
          // 设置渲染方式（有两种方式 table和canvas，默认是canvas）
          // background: '#eeeeee',
          // foreground: '#FF00FF'
        });
        console.log(qrcode);
      },
      refreshStep() {
        if (!this.userInfo.tel) {
          this.vactiveStep = 0;
        } else if (this.userInfo.team.mems) {
          if (this.userInfo.team.mems.length >= 3) {
            this.vactiveStep = 1;
            this.vactiveStep += this.userInfo.stage;
          }
        } else {
          this.vactiveStep = 1;
        }
      }
    },
    created() {
      const that = this;
      this.debouncedGetTeam = debounce(this.getTeam, 500);
      if (!localStorage.getItem("isLogin")) {
        console.log("jumpToLogin");
        this.$router.push("Login");
        return;
      }
      window.onresize = () => {
        return (() => {
          window.fullHeight = document.documentElement.clientHeight;
          that.fullHeight = window.fullHeight;
          that.isPc = that.fullWidth > 1000;
        })();
      };
      //请求刷新组队数据
      this.$axios
        .post(this.url + "get_team")
        .then(res => {
          console.log(res);
          if (res.data.isOk) {
            console.log("获取信息成功");
            this.userInfo = res.data.userInfo;
            this.openDel = false;
            //刷新step
            this.refreshStep();
            this.loading1 = false;
          } else {
            console.log("获取信息失败，请检查是否有授权信息");
            this.show_toast(res.data.errmsg, 1);
            localStorage.setItem("isLogin", "");
            this.$router.push("Login");
            this.loading1 = false;
          }
        })
        .catch(res => {
          console.log(res);
          this.show_toast("服务器连接失败！", 1);
          this.loading1 = false;
        });
    }
  };

</script>

<style>
  .demo-vsteper-container {
    max-width: 24rem;
    max-height: 100rem;
    margin: auto;
    margin-left: 2.5rem;
  }

  .demo-step-content {
    margin: 0 16px;
  }

  .demo-step-button {
    margin-top: 12px;
    margin-right: 12px;
  }

  .msg {
    padding: 1rem;
    margin-top: 0.5rem;
    margin-bottom: 0.5rem;
  }

  .title-name {
    padding-bottom: 0;
  }

  .avatar-member {
    /* padding-left: 3rem; */
    /* padding-right: 0.5rem; */
    min-width: 40px;
  }

  .mu-demo-form {
    padding-bottom: 0;
  }

  .lastone {
    margin-bottom: 0;
  }

  /* .mu-dialog-body{
    padding-bottom: 0;
} */
  /* 解决按钮样式问题 */
  .mu-dialog-actions {
    padding-bottom: 15px;
    padding-right: 15px;
  }

  @media screen and (max-width: 1200px) {
    .mu-tab {
      min-width: 100px;
    }
  }

  @media screen and (min-width: 500px) {
    .card-info {
      margin-left: 2rem;
      margin-top: 1rem;
      width: 100%;
      max-width: 375px;
      min-width: 250px;
    }

    .card-main {
      margin: 2rem 0;
      padding: 2rem 1rem;
    }
  }

  @media screen and (max-width: 500px) {
    .demo-vsteper-container {
      margin: auto;
      margin-top: 2rem;
    }

    .card-info {
      width: 100%;
      max-width: 450px;
    }

    .card-main {
      margin: 1rem 0;
      padding: 2rem 2rem;
    }
  }

</style>
