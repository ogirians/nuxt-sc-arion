<template>
    <div>
        <v-container>
            <v-card 
                class="d-flex mb-6"
                flat
                tile
                style="background-color: #f8fffd;"
            >
                <h3 class="pa-2 mr-auto">Arion today</h3>
                <v-btn
                class="mx-1"
                fab
                dark
                small
                elevation="0"
                color="primary"
                >
                <v-icon dark>
                    mdi-magnify
                </v-icon>
                </v-btn>
                <v-btn
                class="mx-1"
                fab
                dark
                small
                elevation="0"
                color="primary"
                >
                <v-icon dark>
                    mdi-bell
                </v-icon>
                </v-btn>
            </v-card>
        </v-container>
        <v-sheet
            class="mx-auto"
            flat
            style="background-color: #f8fffd;"
        >
            <v-slide-group
            active-class="success"
            >
            <v-slide-item
                v-for="data in arion_data"
                v-slot="{ active, toggle }"
            >
                <v-card
                :color="active ? undefined : 'primary'"
                class="pa-4 ma-2 rounded-xl"
                height="150"
                width="300"
                flat
                dark
                >
                    <div>
                        <v-img 
                            src="/card_background.jpg" 
                            max-height="150"
                            max-width="300"
                            style="position: absolute; top: 0px; right: 0px; opacity: 0.2; border-radius:10px">
                        </v-img>
                    </div>
                    <div style="position: absolute; z-index: 1;">
                        <h3>{{data.head}}</h3> 
                        <br>
                        <h1>{{data.number}}</h1>
                        <h5>{{data.percentage}}</h5>
                    </div>
                </v-card>
            </v-slide-item>
            </v-slide-group>
        </v-sheet>
        <v-container>
            <v-card
                class="py-2 px-4 rounded-xl mt-2"
                flat
                outlined
            >
                <v-card 
                    class="d-flex mb-2 mt-2"
                    flat
                    tile
                >
                    <h3 class="pa-2 mr-auto">Team Member</h3>
                    <v-btn
                    class="mx-1 mt-2 mr-2 text-capitalize"
                    dark
                    small
                    elevation="0"
                    color="primary"
                    >
                    see all
                    </v-btn>
                    
                </v-card>
                <v-card 
                    class="d-flex mb-3 mt-1"
                    flat
                    tile
                >
                    <div class="pa-2 mr-auto">
                        <v-avatar
                        color="primary"
                        size="36"
                        class=""
                        outline
                        >BT</v-avatar>
                        <v-avatar
                        color="error"
                        size="36"
                        class="ml-n2"
                        >AA</v-avatar>
                        <v-avatar
                        color="warning"
                        size="36"
                        class="ml-n2"
                        >OR</v-avatar>
                        <v-avatar
                        color="success"
                        size="36"
                        class="ml-n2"
                        >WN</v-avatar>
                        <v-avatar
                        color="pink"
                        size="36"
                        class="ml-n2"
                        >AL</v-avatar>
                        +2 more
                    </div>
                    <v-btn
                    class="mx-1 mt-1 mr-2 text-capitalize"
                    dark
                    fab
                    small
                    elevation="0"
                    color="black"
                    outlined
                    >
                        <v-icon>mdi-plus</v-icon>
                    </v-btn>
                    
                </v-card>
            </v-card>
        </v-container>
        <v-container class="pb-0 pt-0">
            <v-card 
                class="d-flex mb-0 mt-2"
                flat
                tile
                style="background-color: #f8fffd;"
            >
                <div class="pa-2 mr-auto">
                    <h3>Activity</h3>
                    <h5 style="color:grey">today, 02 Agustus 2024</h5>
                </div>
                
                <v-btn
                class="mx-1 mt-3 mr-2 text-capitalize"
                dark
                small
                elevation="0"
                color="primary"
                >
                latest
                </v-btn>
            </v-card>
        </v-container>
        <v-sheet
            class="mx-auto mt-2"
            style="background-color: #f8fffd;"
        >
            <v-slide-group
            >
            <v-slide-item
                v-slot="{ active, toggle }"
            >
                <v-btn
                class="mx-1 text-capitalize"
                :input-value="active"
                active-class=""
                depressed
                rounded
                @click="toggle"
                small
                
                >
                 Sales Contracts
                </v-btn>
            </v-slide-item>
            <v-slide-item
                v-slot="{ active, toggle }"
            >
                <v-btn
                class="mx-1 text-capitalize"
                :input-value="active"
                active-class=""
                depressed
                rounded
                @click="toggle"
                small
                
                >
                 Invoices
                </v-btn>
            </v-slide-item>
            </v-slide-group>
        </v-sheet>
        <v-container>
            <v-card
                class="py-2 px-4 rounded-xl mt-2"
                flat
                outlined
                v-for = "data in sc_data"
            >
                <v-card 
                    class="d-flex mb-3 mt-2"
                    flat
                    tile
                >
                    <div class="pa-2">
                        <v-avatar
                        color="primary"
                        size="36"
                        class="mr-2"
                        outline
                        >
                        <v-icon>mdi-account</v-icon>
                        </v-avatar>
                    </div>
                    <div class="mr-auto mt-2" style="text-align: start;">
                        <h4>{{ data.customer }}</h4>
                        <h6>{{ data.tanggal_sc }}</h6>
                    </div>
                </v-card>
                <v-divider></v-divider>
                <v-card 
                    class="d-flex mb-1 mt-1"
                    flat
                    tile
                >
                    <v-chip small :color="data.tipe == 'Invoice' ? 'success' : 'warning'" class="mt-2 mr-auto">{{ data.tipe }}</v-chip>
                    <div>
                        <h4 class="my-2 py-0">{{ data.total }}</h4>
                    </div>
                </v-card>
            </v-card>
        </v-container>
    </div>
</template>

<script>
export default{
    data(){
        return {
            arion_data : [
                {
                    head : "Sales Contract",
                    number : "10",
                    percentage : "-10% dari sebelumnya"
                },
                {
                    head : "Pending Invoice",
                    number : "5",
                    percentage : "-5% dari sebelumnya"
                },
                {
                    head : "Potensi pendapatan",
                    number : "Rp 777.777.777",
                    percentage : "+ 1% dari sebelumnya"
                },
                {
                    head : "Pajak",
                    number : "Rp 666",
                    percentage : "-1% dari sebelumnya"
                }
            ],
            sc_data : [
                {
                    customer : "Pt Tri Tunggal Teladan",
                    tanggal_sc : "2024-08-02",
                    tipe : "Sales contract",
                    total : "Rp. 76.000.000"
                },
                {
                    customer : "PT BERKAH BAJA TIMUR",
                    tanggal_sc : "2024-08-02",
                    tipe : "Invoice",
                    total : "Rp. 76.000.000"
                },
                {
                    customer : "Pt Tri Tunggal Teladan",
                    tanggal_sc : "2024-08-02",
                    tipe : "Sales contract",
                    total : "Rp. 76.000.000"
                }
            ]    
        }
    }
}

</script>