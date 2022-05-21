<template>
  <form class="container" @submit.prevent="submit">
    <BaseInput
      v-if="localLogin"
      ref="username"
      name="Email"
      label="F1TV Email"
      tip="Required"
      tipcolor="is-danger"
      icon="ri-user-line"
      success-icon="ri-check-line"
      failure-icon="ri-alert-line"
      type="email"
      v-model="username"
      required
    />
    <BaseInput
      v-if="localLogin"
      ref="password"
      name="Password"
      label="F1TV Password"
      tip="Required"
      tipcolor="is-danger"
      icon="ri-lock-2-line"
      success-icon="ri-check-line"
      failure-icon="ri-alert-line"
      type="password"
      v-model="password"
      required
    />
    <p v-if="authError" class="help is-danger">{{ authError.message }}</p>
    <button type="submit" class="button is-success" :class="{ 'is-loading': loading }" :disabled="loading">
      Login
    </button>
    <p class="content">
      <b>NOTE:</b> This login involves processing of reese84 cookie, which is experimental and may break at any time.
      <a :href="homepage">F1 Web Viewer</a> GitHub repository
    </p>
  </form>
</template>

<script>
  import { mapGetters } from "vuex";

  import BaseInput from "@/components/BaseInput";

  export default {
    name: "Login",
    components: {
      BaseInput
    },
    data() {
      return {
        localLogin: !window.SERVER_LOGIN,
        username: localStorage.username || "",
        password: localStorage.password || "",
        loading: false,
        homepage: process.env.VUE_APP_HOMEPAGE
      };
    },
    computed: {
      ...mapGetters(["authenticated", "authError"])
    },
    methods: {
      submit() {
        if (this.loading) return;

        if (window.SERVER_LOGIN) {
          this.loading = true;

          this.$store
            .dispatch("authenticate", {
              username: "",
              password: ""
            })
            .finally(() => {
              this.loading = false;
            });
        } else {
          const username = this.$refs.username;
          const password = this.$refs.password;

          username.validate();
          password.validate();

          if (username.valid && password.valid) {
            this.loading = true;

            this.$store
              .dispatch("authenticate", {
                username: this.username,
                password: this.password
              })
              .finally(() => {
                localStorage.username = this.username;
                localStorage.password = this.password;
                this.loading = false;
              });
          }
        }

      }
    }
  };
</script>

<style lang="scss" scoped>
  .container {
    padding: 1em;
    padding-top: 0;
  }

  .field:nth-child(1) {
    margin-top: 0 !important;
  }

  .button {
    width: 100%;
    margin-top: 1.75em;
  }

  .content {
    margin-top: 1.75em;
  }
</style>
