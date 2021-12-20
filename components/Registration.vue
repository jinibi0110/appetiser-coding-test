<template>
  <div>
    <div class="container">
      <Loading v-if="showLoading" />
      <div>
        <b-card class="bcard-login">
          <h6 class="title noselect mb-4">CREATE ACCOUNT</h6>

          <b-form
            class="mt-4"
            @submit.prevent="registerAccount"
            id="registration_form"
          >
            <input
              id="email_address"
              class="email noselect"
              v-model="user.email"
              v-on:keyup.enter="registerAccount()"
              placeholder="Email Address"
              required
            />

            <input
              id="full_name"
              class="fullname noselect"
              v-model="user.full_name"
              v-on:keyup.enter="registerAccount()"
              placeholder="Full Name"
              required
            />

            <b-input-group-append class="mb-1">
              <input
                id="password"
                class="password noselect"
                v-model="user.password"
                placeholder="Password"
                :type="passwordType"
                v-on:keyup.enter="registerAccount()"
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

            <b-input-group-append class="mb-1">
              <input
                id="password_conf"
                class="password noselect"
                v-model="user.password_confirmation"
                placeholder="Confirm Password"
                :type="passwordType"
                v-on:keyup.enter="registerAccount()"
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
              id="btn_register"
              class="btn-submit"
              variant="success"
              block
              pill
              @click="registerAccount()"
              >REGISTER</b-btn
            >
          </b-form>

          <p class="subtitle noselect" style="margin-top: 30px">
            Already have account?
            <a class="subtitle noselect" href="/Login"><u>Login here</u></a>
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
        full_name: "",
        email: "",
        password: "",
        password_confirmation: "",
      },

      alert: {
        showAlert: 0,
        dismissSecs: 0,
        variant: "success",
        message: "",
      },
    };
  },

  computed: {},

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

    async registerAccount() {
      this.showLoading = true;
      await axios({
        method: "POST",
        url: "https://api.baseplate.appetiserdev.tech/api/v1/auth/register",
        data: {
          full_name: this.user.full_name,
          email: this.user.email,
          password: this.user.password,
          password_confirmation: this.user.password_confirmation,
        },
      })
        .then((result) => {
          if (result.data.success === true) {
            this.$router.push("/Verification");
          }
          this.showLoading = false;
        })
        .catch((error) => {
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
