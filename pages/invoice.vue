<template>
    <v-container>
        <v-card class="logo" color="primary" elevation="5">
            <v-card-title>
                <span style="color:white" class="mr-5"> 
                RIWAYAT
                </span>
                <v-spacer></v-spacer>
                <v-btn small rounded color="success" class="mr-3" @click="open_form_page()">
                <v-icon small>mdi-plus</v-icon>
                <span v-if="$vuetify.breakpoint.name == 'md'">sales contract</span>
                </v-btn>
            </v-card-title>
        </v-card>      
    </v-container>
</template>