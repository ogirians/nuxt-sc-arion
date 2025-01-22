<template>
    <v-container class="fill-height d-flex flex-column justify-center align-center">
    <v-card min-width="300" class="rounded-lg " color="primary"  elevation="5">
         <v-img 
              src="/card_background.jpg" 
              max-height="100"
              max-width="100%"
              style="position: absolute; top: 0px; right: 0px; opacity: 0.2; border-radius:10px">
          </v-img>
        <v-card-title class="text-center justify-center" style="color:white">LOGIN</v-card-title>
    </v-card>
    
    <v-card min-width="300" class="rounded-lg" elevation="5">
        <v-card-text>
        <v-form ref="form" v-model="valid">
            <v-text-field
            v-model="email"
            label="Email"
            :rules="emailRules"
            required
            ></v-text-field>
            <v-text-field
            v-model="password"
            label="Password"
            type="password"
            :rules="passwordRules"
            required
            ></v-text-field>
            <v-btn :loading="loading_login" class="mt-5" color="primary" block :disabled="!valid || loading_login" @click="submitForm">
                    Login
            </v-btn>
        </v-form>
        <div v-if="error" style="color:red">{{ error }}</div>
        </v-card-text>
        <v-card-actions>
        <v-spacer></v-spacer>
        </v-card-actions>
    </v-card>
    </v-container>
</template>
<script>
export default{
    layout: 'no_menus',
    data(){        
    return {
          email: '',
          password: '',
          valid: false,
          emailRules: [
            v => !!v || 'Email is required',
            v => /.+@.+\..+/.test(v) || 'E-mail must be valid',
          ],
          passwordRules: [
            v => !!v || 'Password is required',
            v => v.length >= 6 || 'Password must be at least 6 characters',
          ],
          error : '',
          loading_login : false
        }
    },
    computed : {
        form_login(){
            const form = {
                email : this.email,
                password : this.password,
                device_name : 'admin-arion'
            }

            return form;
        }
    },
    methods: {
        submitForm() {
        this.loading_login = true;
          if (this.$refs.form.validate()) {
            this.$axios.post('/auth', this.form_login)
            .then(response => {
                console.log(response.data);
                this.loading_login = false;
                this.$router.push({path : '/'});
                localStorage.setItem('arn_tkn', response.data);
                this.$axios.setToken(response.data, 'Bearer');
            })
            .catch(error => {
              this.loading_login = false;
              console.log(error.response);
              this.error = error.response.data.message
            })
          }
        },
        register() {
          alert('Redirecting to registration page...');
        },
      }, 
}
</script>