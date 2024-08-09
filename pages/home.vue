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
                @click="get_activity('')"
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
                v-model="selected_activity"
                @change="get_activity('/'+selected_activity)"
            >
            <v-slide-item
                v-slot="{ active, toggle }"
                value = "sales_contract"
            >
                <v-btn
                class="mx-1 text-capitalize"
                :input-value="active"
                active-class="purple white--text"
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
                value = "invoice"
            >
                <v-btn
                class="mx-1 text-capitalize"
                :input-value="active"
                active-class="purple white--text"
                depressed
                rounded
                @click="toggle"
                small
               
                >
                 Invoices
                </v-btn>
            </v-slide-item>
            <v-slide-item
                v-slot="{ active, toggle }"
                value = "add"
            >
                <v-btn
                class="mx-1 text-capitalize"
                :input-value="active"
                active-class="purple white--text"
                depressed
                rounded
                @click="toggle"
                small
                
                >
                 add
                </v-btn>
            </v-slide-item>
            <v-slide-item
                v-slot="{ active, toggle }"
                value = "delete"
            >
                <v-btn
                class="mx-1 text-capitalize"
                :input-value="active"
                active-class="purple white--text"
                depressed
                rounded
                @click="toggle"
                small
                
                >
                 delete
                </v-btn>
            </v-slide-item>
            <v-slide-item
                v-slot="{ active, toggle }"
                value = "update"
            >
                <v-btn
                class="mx-1 text-capitalize"
                :input-value="active"
                active-class="purple white--text"
                depressed
                rounded
                @click="toggle"
                small
                
                >
                 update
                </v-btn>
            </v-slide-item>
            </v-slide-group>
        </v-sheet>
        <v-container>
            <div v-if = "proses_get_activity == true" class="d-flex justify-center mt-5">
                <v-progress-circular
                indeterminate
                color="primary"
                ></v-progress-circular>
            </div>
            <div v-if = "ada_activities == false" class="d-flex justify-center mt-5">
                <h5>tidak ada aktifitas terbaru</h5>
            </div>
            <div v-else>
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
                            <h6>{{ data.tanggal_sc | tanggal_id }} - {{ data.no_sc }}</h6>
                        </div>
                    </v-card>
                    <v-divider></v-divider>
                    <v-card 
                        class="d-flex mb-1 mt-1"
                        flat
                        tile
                    >
                        <div class="mr-auto">
                            <v-chip small :color="data.tipe == 'invoice' ? 'success' : 'warning'" class="mt-2 mr-auto">{{ data.tipe }}</v-chip>
                            <v-chip small :color='data.color' class="mt-2 mr-auto">{{ data.action }}</v-chip>
                        </div>
                        
                        <div>
                            <h4 class="my-2 py-0">{{ data.total | rupiah}}</h4>
                        </div>
                    </v-card>
                </v-card>
            </div>
            
        </v-container>
    </div>
</template>

<script>
import moment from 'moment';

export default{
    mounted() {
        this.html2pdf = require('html2pdf.js');
    // use html2pdf here
        this.get_home_data();
        this.get_activity();
    },
    data(){
        return {
            selected_activity : '',
            ada_activities : true,
            proses_get_activity : false,
            home_data :'',
            arion_data : [
                {
                    head : "Sales Contract",
                    number : "xx",
                    percentage : "-xx% dari sebelumnya"
                },
                {
                    head : "Pending Invoice",
                    number : "xx",
                    percentage : "-xx% dari sebelumnya"
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
            sc_data : [],    
            sc_data_form : [
                {
                    customer : "Pt Tri Tunggal Teladan",
                    tanggal_sc : "2024-08-02",
                    tipe : "Sales contract",
                    total : "Rp. 76.000.000",
                    no_sc : "SC/APS/002/04/2024",
                    action : 'add',
                    color : 'success'
                },
                {
                    customer : "PT BERKAH BAJA TIMUR",
                    tanggal_sc : "2024-08-02",
                    tipe : "Invoice",
                    total : "Rp. 76.000.000",
                    no_sc : "SC/APS/003/04/2024",
                    action : 'add',
                    color : 'success'
                },
                {
                    customer : "Pt Tri Tunggal Teladan",
                    tanggal_sc : "2024-08-02",
                    tipe : "Sales contract",
                    total : "Rp. 76.000.000",
                    no_sc : "SC/APS/004/04/2024",
                    action : 'add',
                    color : 'success'
                }
            ]    
        }
    },
    methods: {
        get_home_data(){
            this.$axios.get('/home')
            .then(response => {
                this.home_data = response.data.data
                this.arion_data[0].number = this.home_data.sc_count
                this.arion_data[0].percentage = (this.home_data.sc_percent > 0 ? '+' : '') + this.home_data.sc_percent + '% dari bulan sebelumnya'

                this.arion_data[1].number = this.home_data.pend_inv_count
                this.arion_data[1].percentage = (this.home_data.pend_inv_percent > 0 ? '+' : '') + this.home_data.pend_inv_percent + '% dari bulan sebelumnya'

            })
            .catch(error => {
                console.log(error);
            })
        },
        get_activity(kategori = ''){
            this.sc_data = [];
            this.proses_get_activity = true;
            let get_data = '';
            let isi = '';
            this.$axios.get('/home/activity'+kategori)
            .then(response => {
                
                get_data = response.data.data;

                if(get_data.length > 0){
                    get_data.forEach(data => {
                        switch (data.action) {
                            case 'add':
                                data.color = 'success';
                                break;
                            case 'update':
                                data.color = 'info';
                                break;
                            case 'delete':
                                data.color = 'error';
                                break;
                            default:
                                data.color = 'secondary'; // Optional: handle cases where the action doesn't match any case
                                break;
                        }
    
    
    
                        isi = {
                            customer : data.sales_contract.customer.name,
                            tanggal_sc : data.sales_contract.tanggal_sc,
                            tipe : data.kategori,
                            total : data.sales_contract.total,
                            no_sc : data.sales_contract.nomor_sc,
                            action : data.action,
                            color : data.color
                        }
    
                        this.sc_data.push(isi);
                        this.proses_get_activity = false;
                    
                    });
                }else{
                    this.proses_get_activity = false;
                    this.ada_activities = false;
                }

            })
            .catch(error => {
                console.log(error);
                this.proses_get_activity = false;
            })
        }
    },
    computed :{
       
    },
    filters : {
        rupiah(value){
        return Intl.NumberFormat('id', { style: 'currency', currency: 'IDR' }).format(value)
        },
        tanggal_id(value){
        let date_id = moment(value).format('DD-MM-YYYY');
        return  date_id;
        }
    },
}

</script>