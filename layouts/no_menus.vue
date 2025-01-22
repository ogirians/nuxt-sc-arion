<template>
    <v-app dark>
      <v-app-bar
        :clipped-left="clipped"
        fixed
        app
        elevation="0"
        color="white"
      >
      <v-spacer />
      <v-img
          lazy-src="arion"
          max-height="60"
          max-width="60"
          src="/arion.png"
      ></v-img>
      <v-spacer />
      
      </v-app-bar>
      <v-main class="mb-15" style="background-color: #f8fffd;">
  
        <v-container class="px-0">
          <Nuxt />
          
        </v-container>
        
      </v-main>
      <!-- <v-bottom-navigation
        v-model="bottom"
        :background-color="color"
        shift
        fixed
        grow
        color="#6da4ab"
      >
        <v-btn :to="items[0].to" grow style="height: 100%;">
          <span>Contracts</span>
          <v-icon>mdi-lead-pencil</v-icon>
        </v-btn>
  
        <v-btn :to="items[1].to" grow style="height: 100%;">
          <span>Invoices</span>
  
          <v-icon>mdi-calculator</v-icon>
        </v-btn>
        <v-btn :to="items[2].to" grow style="height: 100%;">
          <span></span>
          
          <v-icon>mdi-home</v-icon>
        </v-btn>
        <v-btn :to="items[3].to" grow style="height: 100%;"> 
          <span>Rekap</span>
  
          <v-icon>mdi-book</v-icon>
        </v-btn>
  
        <v-btn :to="items[4].to" grow style="height: 100%;">
          <span>Coming</span>
  
          <v-icon>mdi-image</v-icon>
        </v-btn>
      </v-bottom-navigation> -->
      <!-- <v-footer
        :absolute="!fixed"
        app
      >
        <span>&copy; {{ new Date().getFullYear() }}</span>
      </v-footer> -->
    </v-app>
  </template>
  
  <script>
  export default {
    transition(to, from) {
      if (!from) {
        return 'slide-left'
      }
      return +to.query.page < +from.query.page ? 'slide-right' : 'slide-left'
    },
    
    mounted(){
    this.token = localStorage.getItem('arn_tkn');
    if(this.token){
      this.$axios.setToken(this.token, 'Bearer');

      this.$axios.get('/user')
      .then(response => {
        console.log(response.data);
        this.$router.push({path : '/'});
      })
      .catch(error =>{
        console.log('gagal auth');
        this.$router.push({path : '/login_page'});
      })
    }else {
      this.$router.push({path : '/login_page'});
    }

   
  },
  
    name: 'DefaultLayout',
    data () {
      return {
        clipped: false,
        drawer: false,
        fixed: false,
        bottom : 0,
        items: [
          {
            icon: 'mdi-apps',
            title: 'generate sales contract',
            to: '/sales_contract'
          },
          
          {
            icon: 'mdi-apps',
            title: 'generate Invoice',
            to: '/invoice'
          },
          {
            icon: 'mdi-apps',
            title: 'generate sales contract',
            to: '/'
          },
          {
            icon: 'mdi-apps',
            title: 'Rekap penjualan',
            to: '/rekap_penjualan'
          },
          {
            icon: 'mdi-chart-bubble',
            title: 'generate lain-lain',
            to: '/inspire'
          }
        ],
        miniVariant: false,
        right: true,
        rightDrawer: false,
        title: 'Arion Panca Sekawan'
      }
    },
    computed: {
        color () {
          switch (this.bottom) {
            case 0: return 'white'
            case 1: return 'white'
            case 2: return 'white'
            case 3: return 'white'
            default: return 'white'
          }
        },
      },
  }
  </script>
  