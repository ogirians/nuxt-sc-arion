<template>
  <v-app dark>
    <!-- <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
    >
      <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <v-list-item-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>{{ item.title }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer> -->
    <!-- <v-app-bar
      :clipped-left="clipped"
      fixed
      app
    >
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />            
      <v-toolbar-title>{{ title }}</v-toolbar-title>
      <v-spacer />      
    </v-app-bar> -->
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
    <v-main class="mb-15 mt-5">

      <v-container>
        <Nuxt />
        
      </v-container>
      
    </v-main>
    <v-bottom-navigation
      v-model="bottom"
      :background-color="color"
      dark
      shift
      fixed
      grow
      rounded-lg 
      rounded-b-0
    >
      <v-btn :to="items[0].to">
        <span>Contracts</span>
        <v-icon>mdi-lead-pencil</v-icon>
      </v-btn>

      <v-btn :to="items[1].to">
        <span>Invoices</span>

        <v-icon>mdi-calculator</v-icon>
      </v-btn>
      <v-btn color="green">
        <span></span>
        
        <v-icon>mdi-plus</v-icon>
      </v-btn>
      <v-btn :to="items[2].to"> 
        <span>Rekap</span>

        <v-icon>mdi-book</v-icon>
      </v-btn>

      <v-btn :to="items[3].to">
        <span>Coming</span>

        <v-icon>mdi-image</v-icon>
      </v-btn>
    </v-bottom-navigation>
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
          to: '/'
        },
        {
          icon: 'mdi-apps',
          title: 'generate Invoice',
          to: '/invoice'
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
          case 0: return 'blue-grey'
          case 1: return 'blue-grey'
          case 2: return 'blue-grey'
          case 3: return 'blue-grey'
          default: return 'blue-grey'
        }
      },
    },
}
</script>
