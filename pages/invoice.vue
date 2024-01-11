<template>
    <v-container>
        <v-card class="logo" color="primary" elevation="5">
            <v-card-title>
                <span style="color:white" class="mr-5"> 
                RIWAYAT INVOICE
                </span>
                <v-spacer></v-spacer>
                <v-btn small rounded color="success" class="mr-3">
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
                  v-model="search_sc"
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
            <!-- <v-dialog v-model="dialog_delete_sc" max-width="500px">
              <v-card>
                <v-card-title class="text-h5">hapus sales contract ini?</v-card-title>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="blue darken-1" text @click="close_dialog_delete()">Batal</v-btn>
                  <v-btn color="blue darken-1" text @click="delete_sales_contract(item)">OK</v-btn>
                  <v-spacer></v-spacer>
                </v-card-actions>
              </v-card>
            </v-dialog> -->
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
              @click="show_sales_contract(item.id , 'edit')"
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
      </v-container>    
    </v-card>      
    </v-container>
</template>

<script>
import moment from 'moment';

    export default {
        mounted(){
          this.get_invoice();
        },
        data(){
          return {
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
              date_sc : '',
              modal2 : false,
              item_invoice : [],
          }
        },
        methods :  {
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