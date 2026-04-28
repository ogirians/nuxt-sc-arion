<template>
    <v-container>
       
        <v-card class="logo" color="primary" elevation="5">
            <v-img 
                src="/card_background.jpg" 
                max-height="200"
                max-width="100%"
                style="position: absolute; top: 0px; right: 0px; opacity: 0.2; border-radius:10px">
            </v-img>
              <v-card-title style="height: 70px;">
                  <span style="color:white ;position: absolute; z-index: 1;" class="mr-5"> 
                  REKAP PENJUALAN
                  </span>
                  <v-spacer></v-spacer>
              </v-card-title>
        </v-card>
        <v-card class="logo py-4 mb-10" elevation="5">
            <v-dialog
            ref="dialog2"
            v-model="modal2"
            :return-value.sync="dates"
            persistent
            width="290px"
            >
                <template v-slot:activator="{ on, attrs2 }">
                    <v-text-field            
                        v-model="dateRangeText"
                        label="tanggal_invoice"
                        prepend-inner-icon="mdi-calendar"
                        readonly
                        v-bind="attrs2"
                        v-on="on"
                        dense
                        outlined
                        class="mx-4 mt-3 py-0"
                        clearable
                    ></v-text-field>
                </template>
                <v-date-picker
                v-model="dates"
                scrollable  
                range
                >
                    <v-spacer></v-spacer>
                    <v-btn
                        text
                        color="primary"
                        @click="modal2 = false"
                    >
                        Cancel
                    </v-btn>
                    <v-btn
                        text
                        color="primary"
                        @click="$refs.dialog2.save(dates); get_rekap()"
                    >
                        OK
                    </v-btn>
                </v-date-picker>
            </v-dialog>   
            <v-card outlined class="mx-3">
                <v-card-title>
                <v-text-field
                    v-model="search"
                    append-icon="mdi-magnify"
                    label="Search"
                    single-line
                    hide-details
                    outlined
                    dense
                ></v-text-field>
                </v-card-title>
                
                <v-data-table
                :headers="headers"
                :items="list_rekap"
                :search="search"
                :loading="loading_rekap"
                >
                <template v-slot:item.total_penjualan = "{ item }">
                {{ item.total_penjualan | rupiah }}
                </template>
                <template v-slot:item.dp_nominal = "{ item }">
                {{ item.dp_nominal | rupiah }}
                </template>
                <template v-slot:item.total_pembelian = "{ item }">
                {{ item.total_pembelian | rupiah }}
                </template>
                <template v-slot:item.dp_percent = "{ item }">
                {{ item.dp_percent || 0 }} %
                </template>
                <template v-slot:item.fees="{ item }">
                    <div v-if="item.fees">
                        <ul class="pl-0" style="list-style: none;">
                            <li v-for="(f, i) in (typeof item.fees === 'string' ? JSON.parse(item.fees) : item.fees)" :key="i">
                                <small><b>{{ f.name }}:</b> {{ f.value | rupiah }}</small>
                            </li>
                        </ul>
                    </div>
                </template>
                <template v-slot:item.dpp = "{ item }">
                {{ item.dpp | rupiah }}
                </template>
                <template v-slot:item.ppn = "{ item }">
                {{ item.ppn | rupiah }}
                </template>
                <template v-slot:item.tanggal_invoice="{ item }">
                {{ item.tanggal_invoice | tanggal_id }}
                </template>

                
                </v-data-table>
            </v-card>
            <div class="d-flex justify-end">
                <v-btn small :disabled="list_rekap.length == 0" @click="export_penjualan()" class="success mx-4 my-4"><v-icon>mdi-content-save-outline</v-icon>export xls</v-btn>
            </div>
        </v-card>
    </v-container>
</template>

<script>
import moment from 'moment';
import { Capacitor } from '@capacitor/core';
import { Filesystem, Directory } from '@capacitor/filesystem';
import { FileOpener } from '@capacitor-community/file-opener';

    export default {
        mounted(){
          this.get_rekap();
        },
        created(){
          this.dates = [this.$moment().format('YYYY-MM-DD'),this.$moment().format('YYYY-MM-DD')];
        },
        data () {
        return {
            loading_rekap : false,
            modal2 : false,
            dates: '',
            search: '',
            headers: [
            // { text: 'No', value: 'No'},
            {
                text: 'Nama Cust',
                align: 'start',
                filterable: true,
                value: 'name',
            },
            { text: 'Nomor Invoice', value: 'nomor_invoice' },
            { text: 'Tanggal invoice', value: 'tanggal_invoice' },
            { text: 'Nomor PO', value: 'nomor_po' },
            { text: 'DP %', value: 'dp_percent' },
            { text: 'DP Nominal', value: 'dp_nominal' },
            { text: 'Total Pembelian', value: 'total_pembelian' },
            { text: 'Fees', value: 'fees' },
            { text: 'Dpp', value: 'dpp' },
            { text: 'Ppn', value: 'ppn' },
            { text: 'Total', value: 'total_penjualan' },
            ],
            list_rekap : [],
        }
        },
        computed: {
            dateRangeText () {
                // return this.dates.join(' ~ ')
                return this.conver_tgl(this.tgl_awal) + ' ~ ' + this.conver_tgl(this.tgl_akhir)
            },
            tgl_awal() {
                return this.dates[0]
            },
            tgl_akhir() {
                return this.dates[1]
            },
        },
        methods : {
            get_rekap(){
                this.list_rekap = [];
                this.loading_rekap = true;
                this.$axios.post('/rekap_penjualan',{tgl_awal : this.tgl_awal, tgl_akhir : this.tgl_akhir})
                .then(response => {
                response.data.data.forEach((x ,index) => {
                  this.list_rekap.push(x);
                })
                 this.loading_rekap= false;
                })
                .catch(error => {
                    console.log(error);
                    this.loading_rekap = false;
                })
            },
            convert_rupiah(value){
                return Intl.NumberFormat('id', { style: 'currency', currency: 'IDR' }).format(value)
            },
            conver_tgl(value){
                let date_id = moment(value).format('DD-MM-YYYY');
                return  date_id;
            },
            export_penjualan(){
                this.loading_rekap = true;
                const filename = 'rekap_penjualan_' + this.tgl_awal + '_' + this.tgl_akhir + '.xlsx';

                this.$axios.post('/export_penjualan', { tgl_awal: this.tgl_awal, tgl_akhir: this.tgl_akhir }, { responseType: 'blob' })
                .then(response => {
                    this.loading_rekap = false;

                    if (Capacitor.getPlatform() === 'android') {
                        // Android-specific handling
                        const reader = new FileReader();
                        reader.readAsDataURL(response.data);
                        reader.onloadend = async () => {
                            const base64Data = reader.result.split(',')[1];
                            try {
                                const file = await Filesystem.writeFile({
                                    path: 'secrets/' + filename,
                                    data: base64Data,
                                    directory: Directory.External,
                                    recursive: true,
                                });
                                await FileOpener.open({
                                    filePath: file.uri,
                                    openWithDefault: true,
                                });
                            } catch (e) {
                                console.error(`Unable to open file: ${e.message}`);
                            }
                        };
                    } else {
                        // Web-specific handling
                        const blob = new Blob([response.data], { type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet' });
                        const url = window.URL.createObjectURL(blob);
                        const link = document.createElement('a');
                        link.href = url;
                        link.setAttribute('download', filename);
                        document.body.appendChild(link);
                        link.click();
                        window.URL.revokeObjectURL(url);
                    }
                })
                .catch(error => {
                    console.log(error);
                    this.loading_rekap = false;
                });
            },
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