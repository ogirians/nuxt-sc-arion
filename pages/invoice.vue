<template>
    <v-container>
        <v-card class="logo" color="primary" elevation="5">
            <v-card-title>
                <span style="color:white" class="mr-5"> 
                RIWAYAT INVOICE
                </span>
                <v-spacer></v-spacer>
                <v-btn @click="isAddingInvoice = true" small rounded color="success" class="mr-3">
                <v-icon small>mdi-plus</v-icon>
                <span v-if="$vuetify.breakpoint.name == 'md'">sales contract</span>
                </v-btn>
            </v-card-title>
        </v-card>
        <v-card class="logo py-4 mb-10" elevation="5">   
      <v-container>
        <v-card outlined>
          <v-data-table
            :headers="headers_sc"
            :items="item_invoice"            
            loading-text="loading data"
            :loading="loading_invoice"
            :items-per-page="pagination.itemsPerPage"
            :page="pagination.page"
            :server-items-length="pagination.itemsLength"
            @pagination="handlePagination"
            :footer-props="{
              'items-per-page-options':[5],
              'disable-items-per-page': true,
            }"
          >
          <!-- :mobile-breakpoint="0"  to disbale vertical row -->
          <template v-slot:top="{ item }">
            <v-row class="mt-3">
              <v-col cols="12" md="4" class="py-0">
                <v-text-field
                  v-model="search_invoice"
                  label="Cari nama atau nomor sales contract..."
                  prepend-inner-icon="mdi-magnify"
                  class="mx-4 py-0"
                  outlined
                  dense
                  clearable
                ></v-text-field>
              </v-col>
              <v-col cols="12" md="4" class="py-0">
                <v-dialog
                  ref="dialog2"
                  v-model="modal2"
                  :return-value.sync="date_sc"
                  persistent
                  width="290px"
                >
                  <template v-slot:activator="{ on, attrs2 }">
                    <v-text-field            
                      v-model="date_sc"
                      label="tanggal_sc"
                      prepend-inner-icon="mdi-calendar"
                      readonly
                      v-bind="attrs2"
                      v-on="on"
                      dense
                      outlined
                      class="mx-4 py-0"
                      clearable
                    ></v-text-field>
                  </template>
                  <v-date-picker
                    v-model="date_sc"
                    scrollable          
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
                      @click="$refs.dialog2.save(date_sc)"
                    >
                      OK
                    </v-btn>
                  </v-date-picker>
              </v-dialog>
              </v-col>
              <v-col cols="12" md="4" class="py-0 mb-3">
                <v-btn @click="search_sales_contract()" elevation="0" color="info" class="ml-3 mb-3 mt-1">
                  cari
                </v-btn>
              </v-col>
            </v-row>
            <v-divider></v-divider>
            <v-dialog v-model="dialog_delete_invoice" max-width="500px">
              <v-card>
                <v-card-title class="text-h5">hapus invoice ini?</v-card-title>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="blue darken-1" text @click="dialog_delete_invoice = false">Batal</v-btn>
                  <v-btn color="blue darken-1" text @click="delete_invoice()">OK</v-btn>
                  <v-spacer></v-spacer>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </template>
          <template v-slot:item.actions="{ item }">
            <v-icon
              small
              class="mr-2"
              color="warning"
              @click="exportToPDF(item.id)"
            >
              mdi-file-pdf-box
            </v-icon>
            <v-icon
              small
              class="mr-2"
              color="success"
              @click="show_invoice(item.id)"
            >
              mdi-pencil
            </v-icon>
            <v-icon
              small
              color="error"
              @click="show_dialog_delete(item.id)"
            >
              mdi-delete
            </v-icon>
          </template>
          <template v-slot:item.sales_contract.total ="{ item }">
            {{ item.sales_contract.total | rupiah }}
          </template>
          <template v-slot:item.tanggal_invoice="{ item }">
            {{ item.tanggal_invoice | tanggal_id }}
          </template>
        
          </v-data-table>
        </v-card>
        <v-overlay
          :absolute="false"
          :value="isAddingInvoice"
        >
          <v-card 
            
            light
          >
            <v-card-title v-if="isEditingInvoice == false">tambah invoice: </v-card-title>
            <v-card-title v-else>edit invoice: </v-card-title>
            <v-divider></v-divider>
            <div class="mx-4 mt-2">
            pilih sales contract :
            </div> 
            <v-col
              class="d-flex pb-0"
              cols="12"
              v-if="isEditingInvoice == false"
            >
              <v-autocomplete
                :items = "items_sc"
                :search-input.sync="search_sc"
                :loading = "loading_sc"                
                v-model = "selected_sc"
                placeholder="nomor sales contract"
                outlined
                dense
                clearable        
                item-text="nomor_sc"
                item-value="id"        
              >
                 <template v-slot:item="{ item }">
                      {{item.nomor_sc}}
                  </template>
              </v-autocomplete>
            </v-col>
            <div
              class="d-flex pb-0 mx-3"
              cols="12"
              v-else
            >
              <v-text-field disabled v-model="noscToUpdate" class="mt-3 py-0" outlined dense></v-text-field>
            </div>
            <div class="mx-4">
            tanggal invoice :
            </div>
            <v-col cols="12" class="py-0">
                <v-dialog
                  ref="dialog2"
                  v-model="modal2"
                  :return-value.sync="date_invoice"
                  persistent
                  width="290px"
                >
                  <template v-slot:activator="{ on, attrs2 }">
                    <v-text-field            
                      v-model="date_invoice"
                      placeholder="tanggal"
                      prepend-inner-icon="mdi-calendar"
                      readonly
                      v-bind="attrs2"
                      v-on="on"
                      dense
                      outlined
                      class="mt-3 py-0"
                      clearable
                    ></v-text-field>
                  </template>
                  <v-date-picker
                    v-model="date_invoice"
                    scrollable          
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
                      @click="$refs.dialog2.save(date_invoice)"
                    >
                      OK
                    </v-btn>
                  </v-date-picker>
              </v-dialog>
            </v-col> 
            <v-divider></v-divider>
            <v-card-actions class="d-flex justify-end">            
              <v-btn
                small
                class=""
                :width="60"
                color="error"
                @click="isAddingInvoice = false; isEditingInvoice= false; noscToUpdate = ''"
                :disabled="loading_simpan"
              >
                <div>batal</div>                
              </v-btn>
              <v-btn
                small
                class=""
                :width="60"
                color="success"
                @click="simpan_invoice()"
                :disabled="loading_simpan"
                v-if="isEditingInvoice == false"            
              >
                <div v-if="loading_simpan == false">simpan</div>
                <div v-else>
                   <v-progress-circular
                      indeterminate
                      color="white"
                      
                      :size="20"
                    ></v-progress-circular>
                </div>
              </v-btn>              
              <v-btn
                small
                class=""
                :width="60"
                color="success"
                @click="update_invoice()"
                :disabled="loading_simpan"
                v-else
              >
                <div v-if="loading_simpan == false">update</div>
                <div v-else>
                   <v-progress-circular
                      indeterminate
                      color="white"
                      
                      :size="20"
                    ></v-progress-circular>
                </div>
              </v-btn>              
            </v-card-actions>          
          </v-card>
        </v-overlay>
      </v-container>    
    </v-card>      
    </v-container>
</template>

<script>
import moment from 'moment';

    export default {
        mounted(){
          this.get_invoice();
          // this.get_sales_contract();
        },
        data(){
          return {
              invoice_id_toupdate : '',
              noscToUpdate: '',
              showInvoiceData : '',             
              isEditingInvoice : false,
              invoice_id_todelete : '',
              dialog_delete_invoice : false,
              loading_simpan : false,
              search_invoice : '',
              loading_sc : false,
              search_sc : '', 
              selected_sc : '',
              isAddingInvoice : false,
              loading_invoice : false,
              pagination: {
                    page: 1, // Current page
                    itemsPerPage: 5,
                    itemsLength:0, // Number of items per page
                  },
              headers_sc : [
                  {
                      text: 'No',
                      align: 'start',
                      sortable: false,
                      value: 'no',
                  },
                  {
                      text: 'No sales contract',
                      align: 'start',
                      sortable: false,
                      value: 'sales_contract.nomor_sc',
                  },
                  {
                      text: 'Nama Customoer',
                      align: 'start',
                      sortable: false,
                      value: 'sales_contract.customer.name',
                  },
                  {
                      text: 'Tanggal Invoice',
                      align: 'start',
                      sortable: false,
                      value: 'tanggal_invoice',
                  },
                  {
                      text: 'total contract',
                      align: 'start',
                      sortable: false,
                      value: 'sales_contract.total',
                  },
                  { text: 'Actions', value: 'actions', sortable: false },
              ],
              search_sc : '',       
              date_invoice : '',
              date_sc : '',
              modal2 : false,
              item_invoice : [],
              items_sc : [],
          }
        },
        computed : {
          form_invoice() {
            const form = {
              sales_contract_id  : this.selected_sc,
              tanggal_invoice : this.date_invoice
            }

            return form
          },
        },  
        methods :  {
          show_dialog_delete(id){
            this.dialog_delete_invoice = true;
            this.invoice_id_todelete = id;
          },
          handlePagination(pagination) {
            if(this.pagination.page != pagination.page){
              this.loading_invoice = true;
              this.pagination.page = pagination.page;             
              this.items_invoice = [];
              console.log(pagination)
              this.$axios.get('/invoice?page='+pagination.page)
              .then(response => {
                  // console.log(response);
                  response.data.data.data.forEach((x ,index) => {
                      if(index == 0){
                        x.no = response.data.data.from ;
                      }else{
                        x.no = response.data.data.from + index ;
                      }
                      this.item_invoice.push(x);
                  })
                  this.pagination.itemsLength = response.data.data.total
                  this.loading_sc = false;
                })
                .catch(error => {
                  console.log(error);
                  this.loading_invoice= false;
                })
            }
          },
          get_invoice(){
            this.loading_invoice = true;
            this.$axios.get('/invoice')
            .then(response => {
              response.data.data.data.forEach((x ,index) => {
                if(index == 0){
                  x.no = response.data.data.from ;
                }else{
                  x.no = response.data.data.from + index ;
                }
                this.item_invoice.push(x);
              })
              this.pagination.itemsLength = response.data.data.total
              this.loading_invoice= false;
            })
            .catch(error => {
              console.log(error);
              this.loading_sc = false;
            })
          },
          search_sales_contract() {       
            this.loading_sc = true;             
            this.items_sc = [];

            this.$axios.post('/sales_contract/search-sc', {search_sc : this.search_sc})
            .then(response => {
              // console.log(response);
              response.data.data.data.forEach((x ,index) => {
                  if(index == 0){
                    x.no = response.data.data.from ;
                  }else{
                    x.no = response.data.data.from + index ;
                  }
                  if(x.invoiced == false){
                    this.items_sc.push(x);

                  }

              })
              this.loading_sc = false;
            })
            .catch(error => {
              console.log(error);              
            })
          },
          simpan_invoice() {
            this.loading_simpan = true;
            this.$axios.post('/invoice', this.form_invoice)
            .then(response => {
              console.log(response.data);
              this.selected_sc = '';
              this.date_invoice = '';
              this.loading_simpan = false;
              this.item_invoice = [];
              this.get_invoice();
              this.isAddingInvoice = false;
            })
            .catch(error => {
              console.log(error.data)
              this.loading_simpan = false;
            })

          },
          delete_invoice() {
            this.$axios.delete('/invoice/'+this.invoice_id_todelete)
            .then(response => {
              this.dialog_delete_invoice = false;
              this.item_invoice = [];
              this.get_invoice();
              console.log('berhasil hapus')
            })
            .catch(error => {
              console.log('gagal')
            })
          },
          show_invoice(id){
            this.isAddingInvoice = true;
            this.isEditingInvoice = true;
            this.invoice_id_toupdate = id;
            let invoice = this.item_invoice.find(x => x.id === id);
            this.noscToUpdate = invoice.sales_contract.nomor_sc;
            // this.$axios.get('/invoice/'+id)
            // .then(response => {
              // this.form_invoice   
              // this.showInvoiceData = response.data;   
              // this.noscToUpdate = response.data.data.sales_contract.nomor_sc;     
            //   console.log(response);
            // })
            // .catch(error => {
            //   console.log('gagal')
            // })
          },
          update_invoice(){
            this.loading_simpan = true;
            this.$axios.put('/invoice/'+this.invoice_id_toupdate, this.form_invoice)
            .then(response => {
              this.loading_simpan = false;
              this.isAddingInvoice = false;
              this.item_invoice = [];
              this.get_invoice();
              console.log('berhasil hapus')
            })
            .catch(error => {
              console.log('gagal')
            })
          }  
        },
        watch :{
          search_sc(value){
              // this.getscs(value);
              value && value !== this.selected_sc.name && value.length % 3 === 0 && this.search_sales_contract();
          },
          selected_sc(value){
              if (value === null){
                this.selected_sc = '';
              }
            }
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