<template>
   <section class="section flex justify-center items-center">
      <div class="bg-gray-100 shadow-2xl p-6 rounded">
         <div class="columns">
            <div class="column is-4 is-offset-4">
               <h2 class="text-center text-2xl mb-4">Register</h2>
               <Notification v-if="success" type="success" :message="success" />
               <Notification v-if="error" type="danger" :message="error" />
               <form v-if="!success" method="post" @submit.prevent="register">
                  <div class="field mb-2">
                     <label class="label">Username</label>
                     <div class="control">
                        <input
                           v-model="username"
                           type="text"
                           class="input w-full rounded-sm pl-1"
                           name="username"
                           placeholder="user"
                           required
                           />
                     </div>
                  </div>
                  <div class="field mb-2">
                     <label class="label">Email</label>
                     <div class="control">
                        <input
                           v-model="email"
                           type="email"
                           class="input w-full rounded-sm pl-1"
                           name="email"
                           placeholder="user@comets.js"
                           required
                           />
                     </div>
                  </div>
                  <div class="field mb-2">
                     <label class="label">Password</label>
                     <div class="control">
                        <input
                           v-model="password"
                           type="password"
                           class="input w-full rounded-sm pl-1"
                           name="password"
                           placeholder="password"
                           />
                     </div>
                  </div>
                  <div class="field mb-6">
                     <label class="label">Confirm Password</label>
                     <div class="control">
                        <input
                           v-model="passwordConfirm"
                           type="password"
                           class="input w-full rounded-sm pl-1"
                           name="password"
                           placeholder="password"
                           />
                     </div>
                  </div>
                  <div class="control">
                     <button type="submit" class="bg-blue-400 py-1 w-full rounded-sm font-medium">
                     Register
                     </button>
                  </div>
               </form>
               <div class="font-medium" style="margin-top: 20px">
                  Already got an account? 
                  <nuxt-link to="/login" class="text-blue-600">Login</nuxt-link>
               </div>
            </div>
         </div>
      </div>
   </section>
</template>
<script>
import Notification from "~/components/Notification";

export default {
    components: {
        Notification,
    },
    //Used for collecting data from the input fields
    data(){
        return {
            username: "",
            email: "",
            password: "",
            passwordConfirm: "",
            success: null,
            error: null,
        };
    },
    //This is for checking if the user is logged in then block access to register and login pages
    middleware: "guest",

    methods: {
        async register() {
            this.error = null;
            if(this.password !== this.passwordConfirm){
               this.error = "Passwords do not match.";
               return;
            }
            try {
               this.$axios.setToken(false);
               //Post the user information to Strapi
               await this.$axios.post("/api/auth/local/register", {
                  username: this.username,
                  email: this.email,
                  password: this.password,
               });
               //This doesn't work and it auto confirms the account without confirmation so we can figure out if we want user confirmation or not
               this.success = `A confirmation link has been sent to your email account. Please click on the link to complete the registration process.`;
            }catch (e){
               this.error = "This username or email may already be in use, please try a different username. If this email is already in use, try resetting your password.";
            }
        },
    },
};
</script>
