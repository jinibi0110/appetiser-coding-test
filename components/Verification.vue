<template>
  <div>
    <div class="container">
      <Loading v-if="showLoading" />
      <div>
        <b-card class="bcard-login">
          <h6 class="title noselect">VERIFY ACCOUNT</h6>
          <p class="subtitle noselect">
            Please enter the 5-digit code we sent via email.
          </p>

          <div id="form">
            <input
              type="number"
              maxLength="1"
              size="1"
              min="0"
              max="9"
              pattern="[0-9]{1}"
              @input="inputCode(0)"
              v-model="arr[0]"
              id="i0"
            />

            <input
              type="number"
              maxLength="1"
              size="1"
              min="0"
              max="9"
              pattern="[0-9]{1}"
              @input="inputCode(1)"
              v-model="arr[1]"
              id="i1"
            />

            <input
              type="number"
              maxLength="1"
              size="1"
              min="0"
              max="9"
              pattern="[0-9]{1}"
              @input="inputCode(2)"
              v-model="arr[2]"
              id="i2"
            />

            <input
              type="number"
              maxLength="1"
              size="1"
              min="0"
              max="9"
              pattern="[0-9]{1}"
              @input="inputCode(3)"
              v-model="arr[3]"
              id="i3"
            />

            <input
              type="number"
              maxLength="1"
              size="1"
              min="0"
              max="9"
              pattern="[0-9]{1}"
              @input="inputCode(4)"
              v-model="arr[4]"
              id="i4"
            />

            <b-btn
              id="btn_verify"
              class="btn-submit btn-verify"
              variant="success"
              pill
              @click="verifyAccount()"
              >VERIFY</b-btn
            >
          </div>

          <p class="subtitle noselect" style="margin-top: 30px">
            Didn't receive code?
            <a @click="resendCode()"><u>Resend here</u></a>
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
      pincode: "",
      arr: [],
      passwordType: "password",
      icon: "eye-slash",
      showLoading: false,
      token: "",

      alert: {
        showAlert: 0,
        dismissSecs: 0,
        variant: "success",
        message: "",
      },
    };
  },

  created() {
    this.token = localStorage.token;
  },

  methods: {
    inputCode(v) {
      if (this.arr[v].length == 1) {
        if (v != 4) {
          document.getElementById("i" + (v + 1)).focus();
        }
      } else {
        var str = this.arr[v];
        this.arr[v] = str[0];
      }
    },

    showAlert(message, variant) {
      this.alert = {
        showAlert: 3,
        dismissSecs: 2,

        variant,
        message,
      };
    },

    async verifyAccount() {
      this.showLoading = true;

      await axios({
        method: "POST",
        url: "https://api.baseplate.appetiserdev.tech/api/v1/auth/verification/verify",
        headers: {
          Authorization: `Bearer ${localStorage.token}`,
        },
        data: {
          token: "75632",
          via: "email",
        },
      })
        .then((result) => {
          if (result.data.success === true) {
            this.$router.push("/Login");
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

    async resendCode() {
      this.showLoading = true;

      await axios({
        method: "POST",
        url: "https://api.baseplate.appetiserdev.tech/api/v1/auth/verification/resend",
        headers: {
          Authorization: `Bearer ${localStorage.token}`,
        },
        data: {
          via: "email",
        },
      })
        .then((result) => {
          if (result.data.success === true) {
            this.showAlert("Success! Please check your email", "danger");
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
