<template>
  <section class="login">
    <div v-if="loggedInUser">
      Hello {{ loggedInUser.fullname }}
      <button @click="logout">Logout</button>
    </div>
    <div v-else>
      <button class="close-modal" @click="closeModal">X</button>
      <form v-if="isSignup" class="signup-form" @submit.prevent="signup">
        <h3>Sign up for your account</h3>
        <div class="signup-inputs">
          <label>
            Name:
            <input
              type="text"
              placeholder="Enter full name"
              v-model="signupCred.fullname"
            />
          </label>
          <label>
            Username:
            <input
              type="text"
              placeholder="Enter username"
              v-model="signupCred.username"
            />
          </label>
          <!-- <label>
          Email: 
        <input
          type="email"
          placeholder="Enter email"
          v-model="signupCred.email"
        />
        </label> -->
          <label>
            Password:
            <input
              type="password"
              placeholder="Enter password"
              v-model="signupCred.password"
            />
          </label>
        </div>
        <button>Signup</button>
      </form>
      <form v-else class="login-form" @submit.prevent="login">
        <h3>Log in to your account</h3>
        <div class="login-inputs">
          <label>
            Username:
            <input
              type="text"
              placeholder="Enter username"
              v-model="credentials.username"
            />
          </label>
          <label>
            Password:
            <input
              type="password"
              placeholder="Enter password"
              v-model="credentials.password"
            />
          </label>
        </div>
        <button>Login</button>
      </form>
    </div>
    <!-- <div class="yuumi-container">
      <yuumi></yuumi>
    </div> -->
  </section>
</template>

<script>
// @ is an alias to /src
import { socketService } from "../services/socket-service";
import { userService } from "../services/user-service";
import yuumi from '../cmps/yuumi.vue'

export default {
  components:{
    yuumi,
  },
  props: { isSignup: Boolean },
  data() {
    return {
      credentials: {
        username: "Guest",
        password: "1234",
      },
      signupCred: {
        username: "Big Jimbo",
        password: "1234",
        fullname: "James Smith",
        email: "shimi@yuulo.com",
      },
      // loggedInUser: this.$store.getters.loggedinUser
    };
  },
  async created() {
    //loggs in a user automatically
    // try {
    // const user = await this.$store.dispatch({
    //   type: "login",
    //   userCred: this.credentials,
    // });
    // socketService.emit("user-watch", user._id);
    // this.$store.dispatch({type: 'loadUserCardWatch', userId: user._id})
    // } catch (err) {
    //   console.log("cannot login", err);
    // }
  },
  computed: {
    loggedInUser() {
      return this.$store.getters.loggedinUser;
    },
  },
  methods: {
    closeModal() {
      this.$emit("closeModal");
    },
    async login() {
      try {
        const user = await this.$store.dispatch({
          type: "login",
          userCred: this.credentials,
        });
        this.$store.dispatch({ type: "loadUserCardWatch", userId: user._id });
        this.$emit("closeModal");
      } catch (err) {
        console.log("cannot login", err);
      }
    },
    async signup() {
      try {
        this.signupCred.email = this.signupCred.username + "@yuulo.com";
        const user = await this.$store.dispatch({
          type: "signup",
          userCred: this.signupCred,
        });
        this.$store.dispatch({ type: "loadUserCardWatch", userId: user._id });
        this.$emit("closeModal");
      } catch (err) {
        console.log("cannot signup", err);
      }
    },
    logout() {
      this.$store.dispatch({ type: "logout" });
    },
  },
};
</script>
