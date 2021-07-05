
<template>
  <div class="aasd">
    <table
      width="100%"
      height="600px"
      border="0"
      cellspacing="0"
      cellpadding="0"
      class="login_mbbg"
    >
      <tbody>
        <tr>
          <td valign="top" align="center">
            <table
              width="969px"
              align="center"
              border="0"
              cellspacing="0"
              cellpadding="0"
            >
              <tbody>
                <tr>
                  <td height="228px" class="login1">&nbsp;</td>
                </tr>
                <tr>
                  <td height="113" class="login2">
                    <div
                      style="position: relative; width: 969px; height: 113px"
                    >
                      <div style="position: absolute; left: 515px">
                        <input
                          v-model="userName"
                          tabindex="1"
                          style="
                            border: 1px rgb(128, 128, 128) solid;
                            width: 170px;
                            height: 25px;
                            color: rgb(0, 0, 0);
                            font-size: 18px;
                            font-weight: bold;
                            background-color: rgb(255, 255, 255);
                          "
                          type="text"
                        />
                      </div>
                      <div style="position: absolute; left: 515px; top: 30px">
                        <input
                          v-model="password"
                          tabindex="2"
                          style="
                            border: 1px rgb(128, 128, 128) solid;
                            width: 170px;
                            height: 25px;
                            color: rgb(0, 0, 0);
                            font-size: 18px;
                            font-weight: bold;
                            background-color: rgb(255, 255, 255);
                          "
                          type="password"
                        />
                      </div>
                      <div style="position: absolute; left: 515px; top: 60px">
                        <input
                          tabindex="3"
                          style="
                            border: 1px rgb(128, 128, 128) solid;
                            width: 60px;
                            height: 25px;
                            color: rgb(0, 0, 0);
                            font-size: 18px;
                            font-weight: bold;
                            background-color: rgb(255, 255, 255);
                          "
                          type="text"
                          maxlength="4"
                          v-model="veCode"
                        />
                      </div>
                      <div
                        style="
                          position: absolute;
                          left: 585px;
                          top: 62px;
                          cursor: pointer;
                        "
                        @click="getLoginCode"
                        title="刷新验证码"
                      >
                        <img :src="code" style="width: 100px; height: 25px" />
                      </div>
                      <div style="position: absolute; left: 515px; top: 90px">
                        <input
                          tabindex="4"
                          class="btn"
                          type="button"
                          value=""
                          @click="Login()"
                        />
                      </div>
                      <!-- <div style="position: absolute; left: 580px; top: 90px">
                        <input
                          type="checkbox"
                          value="1"
                          style="cursor: pointer"
                        /><a
                          id="a_ismobile"
                          style="
                            display: inline-block;
                            height: 25px;
                            line-height: 20px;
                            font-size: 12px;
                            cursor: pointer;
                          "
                          >手机登入</a
                        >
                      </div> -->
                    </div>
                  </td>
                </tr>
                <tr>
                  <td height="259" class="login3">&nbsp;</td>
                </tr>
              </tbody>
            </table>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from "axios";
import { MessageBox } from "element-ui";

export default {
  name: "login",

  data() {
    return {
      userName: "",
      password: "",
      ip: "",
      code: "",
      veCode: "",
      state: this.$store.state,
    };
  },
  mounted() {
    if (localStorage.getItem("token")) {
      this.$router.push("/lobby");
    }
    // this.getLoginCode();
  },
  methods: {
    getLoginCode() {
      axios
        .get("/code?sign=967e0fe966313c0f1b7a80df55223738", { responseType: "blob" })
        .then((response) => {
          console.log(response.data);
          this.code = window.URL.createObjectURL(response.data);
        });
    },

    fileReader(data) {
      return new Promise((resolve, reject) => {
        let reader = new window.FileReader();
        reader.readAsArrayBuffer(data);
        reader.onload = function (e) {
          const result = e.target.result;
          resolve(result);
        };
        reader.onerror = function (e) {
          reject(e);
        };
      });
    },
    Login() {
      let _self = this;
      // if (!/^[0-9]*$/.test(this.veCode)) {
      //   MessageBox.alert("请输入正确的验证码", "", {
      //     confirmButtonText: "确定",
      //   });
      //   return;
      // }
      // if (this.veCode.length != 4) {
      //   MessageBox.alert("请输入正确的验证码", "", {
      //     confirmButtonText: "确定",
      //   });
      //   return;
      // }
      axios
        .post("/login?sign=967e0fe966313c0f1b7a80df55223738", {
          username: this.userName,
          password: this.password,
          // checkCode: this.veCode,
        })
        .then(function (res) {
          if (res.data.status == 200) {
            _self.state.isLogin == true;
            _self.state.token == res.data.token;
            _self.state.username == _self.userName;
            localStorage.setItem("token", res.data.response.token);
            localStorage.setItem("modifyFlag", res.data.response.modifyFlag);
            localStorage.setItem("status", res.data.response.status);
            _self.$router.push("/agree");
          } else {
            MessageBox.alert(res.data.message, "", {
              confirmButtonText: "确定",
            });
          }
        })
        .catch(function (err) {
          console.log(err);
        });
    },
  },
};
</script>

<style scoped>
.login1 {
  background-image: url(../../assets/images/login/login_1.jpeg);
}
.login2 {
  background-image: url(../../assets/images/login/login_2.jpeg);
}
.login3 {
  background-image: url(../../assets/images/login/login_3.jpeg);
}
.login_mbbg {
  background-image: url(../../assets/images/login/login_mbbg.jpeg);
}
.btn,
.btn_m {
  width: 60px;
  height: 26px;
  border: 0px solid #ff9224;
  background-color: #ffffff;
  background-image: url(../../assets/images/login/Login_butmb.jpeg);
  cursor: pointer;
  background-position: 0px 0;
}
.btn:hover {
  background-position: 60px 0;
}
.btn :hover {
  width: 60px;
  height: 26px;
  background-image: url(../../assets/images/login/Login_butmb.jpeg);
  background-position: 60px 0;
}
.verification {
  border-radius: 5px;
  width: 100px;
  letter-spacing: 5px;
  margin-left: 10px;
  height: 25px;
  transform: translate(-15px, 0);
  cursor: pointer;
}
</style>
