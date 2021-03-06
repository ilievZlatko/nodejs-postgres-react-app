<template>
  <div class="register">
    <h1>Register</h1>
    <form
      autocomplete="off"
      method="post"
      action="/register"
      @submit.prevent="registerUser"
    >
      <input
        ref="email"
        name="email"
        placeholder="email"
        type="email"
        readonly
        @focus="removeReadOnly"
        v-model="email"
      />
      <input
        ref="password"
        name="password"
        placeholder="password"
        type="password"
        readonly
        @focus="removeReadOnly"
        v-model="password"
      />
      <input
        ref="repeatPassword"
        name="password"
        placeholder="repeat password"
        type="password"
        readonly
        @focus="removeReadOnly"
        v-model="repeatPassword"
      />
      <div>
        Already have an account?<a class="redirect" @click="navigateToSignIn">Login</a>
      </div>
      <br />
      <button
        :disabled="repeatPassword.length < 8 || error"
        type="submit"
        class="btn btn-primary"
      >Register</button>
    </form>
    <div v-if="authError || error" class="error" v-html="authError || error" @click="dismissError"/>
    <div v-if="success" class="success" v-html="'User successfully created! You can now login.'" />
  </div>
</template>

<script>
import Vuex from 'vuex';

export default {
  name: 'register',

  data() {
    return {
      email: '',
      password: '',
      repeatPassword: '',
      success: false,
      error: null,
    };
  },

  computed: {
    ...Vuex.mapState({
      user: state => state.users.user,
      token: state => state.users.token,
      authError: state => state.users.authError,
    }),
  },

  watch: {
    repeatPassword(val) {
      if (val.length < 8) return;
      if (this.password && this.password === val) {
        this.error = null;
        this.dismissError();
      } else {
        this.error = 'Passwords do not match.';
      }
    },

    token() {
      this.showSuccess();
    },
  },

  methods: {
    ...Vuex.mapActions('users', [
      'register',
    ]),

    registerUser() {
      const data = {
        email: this.email,
        password: this.password,
      };

      this.register(data);
      this.dismissError();
    },

    dismissError() {
      setTimeout(() => {
        this.error = null;
      }, 5000);
    },

    showSuccess() {
      this.success = true;
      this.email = '';
      this.password = '';
      this.repeatPassword = '';
      setTimeout(() => {
        this.success = false;
      }, 5000);
    },

    removeReadOnly() {
      this.$refs.email.removeAttribute('readonly');
      this.$refs.password.removeAttribute('readonly');
      this.$refs.repeatPassword.removeAttribute('readonly');
    },

    navigateToSignIn() {
      this.$router.push({ name: 'login' });
    },
  },
};
</script>

<style scoped>
  .register {
    display: flex;
    margin-top: 200px;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }

  form {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }

  h1 {
    margin-bottom: 30px;
  }

  input {
    min-width: 320px;
  }

  .error {
    position: absolute;
    bottom: 60px;
    padding: 15px;
    background: #e45e77;
    color: white;
    border-radius: 10px;
    cursor: pointer;
    user-select: none;
  }

  .success {
    position: absolute;
    bottom: 60px;
    padding: 15px;
    background: #42b983;
    color: white;
    border-radius: 10px;
    cursor: pointer;
    user-select: none;
  }

  .redirect {
    color: #42b983;
    cursor: pointer;
    padding: 10px;
    font-weight: 500;
    user-select: none;
  }

  .redirect:hover {
    color: #35a974;
  }
</style>
