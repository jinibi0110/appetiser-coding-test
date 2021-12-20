<template>
  <div>
    <div class="container">
      <Loading v-if="showLoading" />
      <div>
        <b-card class="bcard-login">
          <h6 class="title noselect mb-4">LOGIN ACCOUNT</h6>

          <b-form class="mt-4" @submit.prevent="loginAccount" id="login_form">
            <input
              id="email_address"
              class="email noselect"
              v-model="user.email"
              v-on:keyup.enter="loginAccount()"
              placeholder="Email Address"
              required
            />

            <b-input-group-append class="mb-1">
              <input
                id="login_password"
                class="password noselect"
                v-model="user.password"
                placeholder="Password"
                :type="passwordType"
                ref="password"
                v-on:keyup.enter="loginAccount()"
              />
              <span
                @mousedown="showPassword()"
                @mouseout="hidePassword()"
                @mouseup="hidePassword()"
                class="field-icon"
              >
                <font-awesome-icon :icon="icon" />
              </span>
            </b-input-group-append>

            <b-btn
              pill
              id="btn_login"
              class="btn-submit"
              @click="loginAccount()"
              variant="success"
              block
              >LOGIN</b-btn
            >
          </b-form>

          <p class="subtitle noselect" style="margin-top: 30px">
            Don't have account yet?
            <a class="subtitle noselect" href="/Registration"
              ><u>Register here</u></a
            >
          </p>

          <div>
            <b-alert
              :show="alert.showAlert"
              dismissible
              fade
              :variant="alert.variant"
              @dismissed="alert.showAlert = null"
            >
              <font-awesome-icon
                :icon="
                  alert.variant == 'success'
                    ? 'check-circle'
                    : 'exclamation-triangle'
                "
                class="mr-1"
                style="font-size: 20px"
              />
              {{ alert.message }}
            </b-alert>
          </div>
        </b-card>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Loading from "~/components/Loading/Loading.vue";

export default {
  components: { Loading },

  data() {
    return {
      passwordType: "password",
      icon: "eye-slash",
      showLoading: false,
      user: {
        email: "",
        password: "",
      },

      alert: {
        showAlert: 0,
        dismissSecs: 0,
        variant: "success",
        message: "",
      },
    };
  },

  methods: {
    showPassword() {
      this.passwordType = "text";
      this.icon = "eye";
    },

    hidePassword() {
      this.passwordType = "password";
      this.icon = "eye-slash";
    },

    showAlert(message, variant) {
      this.alert = {
        showAlert: 3,
        dismissSecs: 2,

        variant,
        message,
      };
    },

    async loginAccount() {
      this.showLoading = true;

      await axios({
        method: "POST",
        url: "https://api.baseplate.appetiserdev.tech/api/v1/auth/login",
        data: {
          username: this.user.email,
          password: this.user.password,
        },
      })
        .then((result) => {
          console.log(result.data);
          if (result.data.success === true) {
            this.$router.push("/Success");
          }
          this.showLoading = false;
        })
        .catch((error) => {
          console.log(error.response);
          this.showLoading = false;
          if (error.response && error.response.data.message) {
            this.showAlert(error.response.data.message, "danger");
          } else {
            this.showAlert(error.message, "danger");
          }
        });
    },
  },
};
</script>
