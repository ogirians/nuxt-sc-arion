<template>
  <v-container>
    <v-card class="logo" color="primary" elevation="5">
        <v-card-title>
            <span style="color:white" class="mr-5"> 
              RIWAYAT SC
            </span>
            <v-spacer></v-spacer>
            <v-btn small rounded color="success" class="mr-3" @click="open_form_page()">
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
            :items="items_sc"
            :items-per-page="pagination.itemsPerPage"
            :page="pagination.page"
            :server-items-length="pagination.itemsLength"
            loading-text="loading data"
            :loading="loading_sc"
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
            <v-dialog v-model="dialog_delete_sc" max-width="500px">
              <v-card>
                <v-card-title class="text-h5">hapus sales contract ini?</v-card-title>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="blue darken-1" text @click="close_dialog_delete()">Batal</v-btn>
                  <v-btn color="blue darken-1" text @click="delete_sales_contract(item)">OK</v-btn>
                  <v-spacer></v-spacer>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </template>
           <template v-slot:item.invoiced="{ item }">

            <v-icon
              v-if="item.invoiced == 1"
              small
              class="mr-2"
              color="success"
            >
              mdi-checkbox-outline
            </v-icon>
             <v-icon
              v-if="item.invoiced == 0"
              small
              class="mr-2"
              color="error"
            >
              mdi-close-box-outline
            </v-icon>
          </template>
          <template v-slot:item.actions="{ item }">
            <v-icon
              small
              class="mr-2"
              color="warning"
              @click="exportToPDF_api(item.id)"
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
              class="mr-2"
              small
              color="error"
              @click="show_dialog_delete(item.id)"
            >
              mdi-delete
            </v-icon>
            <v-icon
              small
              :color="(item.id == selected_sc) ? 'success' : 'secondary'"
              @click="preview_func(item.id)"
            >
              mdi-eye
            </v-icon>
          </template>
          <template v-slot:item.total="{ item }">
            {{ item.total | rupiah }}
          </template>
          <template v-slot:item.tanggal_sc="{ item }">
            {{ item.tanggal_sc | tanggal_id }}
          </template>
        
        </v-data-table>
        </v-card>
      </v-container>    
    </v-card>

    <v-card class="logo" color="primary" elevation="5" :loading="loading_open_form">
        <v-card-title>
            <span style="color:white" class="mr-5"> 
              SALES CONTRACT
            </span>
            <v-spacer></v-spacer>
            <v-btn small rounded color="error" class="mr-3" @click="close_form_page()">
              <v-icon small>mdi-close</v-icon>
              <span v-if="$vuetify.breakpoint.name == 'md'">clear</span>
            </v-btn>
        </v-card-title>
    </v-card>    
    <v-card id="tambah_sc" elevation="5" :loading="loading_open_form">
      <v-container>
        <div v-if="open_sc_form == true">
          <v-card class="logo mt-5" color="secondary">
              <v-card-title>
                  <span style="color:white" class="mr-5"> 
                    Customer
                  </span>
                  <!-- <v-text-field
                    class="mt-3"
                    dark
                    v-model="sales_contract_no"
                  >
                      
                  </v-text-field>           -->
              </v-card-title>
          </v-card>      
          <v-card class="logo py-4" outlined>   
            <v-container>
              <v-form
              id = "form_customer"
              ref="form"
              v-model="valid_customer"
              lazy-validation
              >
                <v-row class="mt-5">
                  <v-col class="py-0" cols="12">
                    <v-autocomplete
                      :rules="[v => !!v || 'Nama wajib diisi']"
                      required
                      label = "cari nama customer"
                      clearable
                      filled
                      v-if="isAdding == false"
                      dense
                      item-text="att.name"
                      item-value="att"
                      :items = "list_customers"
                      :search-input.sync="search_customer"
                      :loading = "loading_customer"
                      v-model = "selected_customer"
                      @change = "updateCustomer()"
                    >
                    <template v-slot:item="{ item }">
                      {{item.att.name}} - {{ item.att.alamat }}
                    </template>
                      <template v-slot:append-outer>
                        <v-slide-x-reverse-transition
                          mode="out-in"
                        >
                          <v-icon
                            :key="`icon-${isAdding}`"
                            :color="isAdding ? 'success' : 'info'"
                            @click="isAdding = !isAdding"
                            v-text="isAdding ? 'mdi-list-box-outline' : 'mdi-plus-circle'"
                          ></v-icon>
                        </v-slide-x-reverse-transition>
                      </template>      
                    </v-autocomplete>
                    <v-text-field
                      label="nama customer baru"
                      placeholder="nama"
                      filled
                      v-if ="isAdding == true"
                      dense
                      v-model="customer.nama"
                      required
                      :rules="[v => !!v || 'nama wajib diisi']"
                    >
                      <template v-slot:append-outer>
                        <v-slide-x-reverse-transition
                          mode="out-in"
                        >
                          <v-icon
                            :key="`icon-${isAdding}`"
                            :color="isAdding ? 'success' : 'info'"
                            @click="isAdding = !isAdding"
                            v-text="isAdding ? 'mdi-list-box-outline' : 'mdi-plus-circle'"
                          ></v-icon>
                        </v-slide-x-reverse-transition>
                      </template>      
                    </v-text-field>       
                  </v-col>
                  <v-col class="py-0" cols="12" md="4">            
                    <v-text-field
                      label="npwp"              
                      filled              
                      dense
                      v-model = "customer.npwp"
                      required
                      :rules="[v => !!v || 'npwp wajib diisi ']"
                    >              
                    </v-text-field>                   
                  </v-col>
                  <v-col class="py-0" cols="12" md="4">            
                    <v-text-field
                      label="alamat"              
                      filled    
                      small         
                      dense
                      v-model = "customer.alamat" 
                      required
                      :rules="[v => !!v || 'alamat wajib diisi']"
                    >              
                    </v-text-field>                   
                  </v-col>
                  <v-col class="py-0" cols="12" md="4">            
                    <v-text-field
                      label="alamat pengambilan"              
                      filled    
                      small         
                      dense 
                      v-model = "customer.alamat_pengambilan"              
                    >              
                    </v-text-field>                   
                  </v-col>
                </v-row>
              </v-form>
            </v-container>    
          </v-card>
          <v-dialog
              ref="dialog"
              v-model="modal"
              :return-value.sync="date"
              persistent
              width="290px"
            >
              <template v-slot:activator="{ on, attrs }">
                <v-text-field            
                  v-model="date"
                  label="tanggal"
                  prepend-icon="mdi-calendar"
                  readonly
                  v-bind="attrs"
                  v-on="on"
                  class="mt-10 col-12 col-md-3"
                ></v-text-field>
              </template>
              <v-date-picker
                v-model="date"
                scrollable          
              >
                <v-spacer></v-spacer>
                <v-btn
                  text
                  color="primary"
                  @click="modal = false"
                >
                  Cancel
                </v-btn>
                <v-btn
                  text
                  color="primary"
                  @click="$refs.dialog.save(date)"
                >
                  OK
                </v-btn>
              </v-date-picker>
          </v-dialog>
          <v-card color="secondary" dark>
             <v-card-title>
              <span>List barang</span>
              <v-spacer></v-spacer>
              <v-btn small rounded color="success" class="mr-3" @click="AddProduct()">
                <v-icon small>mdi-plus</v-icon>
                <span v-if="$vuetify.breakpoint.name == 'md'">item</span>
              </v-btn>
              <v-btn small rounded color="info" @click="isAddingOngkir = true">
                <v-icon small>mdi-truck-flatbed</v-icon>
                <span v-if="$vuetify.breakpoint.name == 'md'"> ongkir</span>
                <v-tooltip
                  top
                  v-if="error_simpan && error_simpan.data.validate_errors.ongkir"
                  color="warning"
                >
                  <template v-slot:activator="{ on, attrs }">
                    <span
                      icon
                      v-bind="attrs"
                      v-on="on"
                    >
                      <v-icon small color="warning">
                        mdi-alert-box
                      </v-icon>
                    </span>
                  </template>
                  <span>{{error_simpan.data.validate_errors.ongkir[0]}}</span>
                </v-tooltip>
              </v-btn>
            </v-card-title>
          </v-card>
          <v-card class="logo" outlined>
            <v-simple-table        
              id = "table-products"
            >
              <template v-slot:default>
                <thead>
                  <tr>
                    <th class="text-left">
                      NO
                    </th>
                    <th class="text-left">
                      JENIS BARANG<br>
                      <v-icon @click="CopyValue('jenis_barang')" small>mdi-list-box-outline</v-icon>
                       persis
                    </th>
                    <th class="text-left">
                      CODE COIL<br>
                      <v-icon @click="CopyValue('code_coil')" small>mdi-list-box-outline</v-icon>
                       persis
                    </th>
                    <th class="text-left">
                      QTY (Kg)<br>
                      <v-icon @click="CopyValue('qty')" small>mdi-list-box-outline</v-icon>
                       persis
                    </th>
                    <th class="text-left">
                      TOTAL (Mtr)<br>
                      <v-icon @click="CopyValue('total_mtr')" small>mdi-list-box-outline</v-icon>
                       persis
                    </th>
                    <th class="text-left">
                      HARGA<br>
                      <v-icon @click="CopyValue('harga')" small>mdi-list-box-outline</v-icon>
                       persis
                    </th>
                    <th class="text-left">
                      TOTAL
                    </th>
                  </tr>
                </thead>
                <tbody>
                  <tr
                    v-for="(item, index) in products" :key = "index"      
                  >
                    <td class style="min-width: 80px;">
                      <span style="display: inline;">{{ index + 1 }}<v-icon @click="RmProduct(index)" small color="error">mdi-minus-circle</v-icon></span>
                    </td>              
                    <td>
                      <div style="display: inline-flex; align-items: center;">
                        <v-tooltip
                          top
                          v-if="error_simpan && error_simpan.data.validate_errors[index] && error_simpan.data.validate_errors[index].jenis_barang"
                          color="warning"
                        >
                          <template v-slot:activator="{ on, attrs }">
                            <span
                              icon
                              v-bind="attrs"
                              v-on="on"
                            >
                              <v-icon small color="warning" class="mr-2">
                                mdi-alert-box
                              </v-icon>
                            </span>
                          </template>
                          <span>{{error_simpan.data.validate_errors[index].jenis_barang[0] }}</span>
                        </v-tooltip>
                         <input style="width: 300px;" type="text" v-model="item.jenis_barang"/>                    
                      </div>
                    </td>              
                    <td>
                      <div style="display: inline-flex; align-items: center;">
                        <v-tooltip
                          top
                          v-if="error_simpan && error_simpan.data.validate_errors[index] && error_simpan.data.validate_errors[index].code_coil"
                          color="warning"
                        >
                          <template v-slot:activator="{ on, attrs }">
                            <span
                              icon
                              v-bind="attrs"
                              v-on="on"
                              style="display: inline;"
                            >
                              <v-icon small color="warning" class="mr-2">
                                mdi-alert-box
                              </v-icon>
                            </span>
                          </template>
                          <span style="display: inline;">{{error_simpan.data.validate_errors[index].code_coil[0] }}</span>
                        </v-tooltip>
                         <input style="width: 100px; display: inline;" type="text" v-model="item.code_coil"/>                    
                      </div>
                    </td>              
                    <td>
                      <div style="display: inline-flex; align-items: center;">
                        <v-tooltip
                          top
                          v-if="error_simpan && error_simpan.data.validate_errors[index] && error_simpan.data.validate_errors[index].qty"
                          color="warning"
                        >
                          <template v-slot:activator="{ on, attrs }">
                            <span
                              icon
                              v-bind="attrs"
                              v-on="on"
                              style="display: inline;"
                            >
                              <v-icon small color="warning" class="mr-2">
                                mdi-alert-box
                              </v-icon>
                            </span>
                          </template>
                          <span style="display: inline;">{{error_simpan.data.validate_errors[index].qty[0] }}</span>
                        </v-tooltip>
                        <input @change="HitungTotal(index)" style="width: 70px;" type="number" v-model="item.qty"/>                    
                      </div>
                    </td>              
                    <td>
                      <div style="display: inline-flex; align-items: center;">
                        <v-tooltip
                          top
                          v-if="error_simpan && error_simpan.data.validate_errors[index] && error_simpan.data.validate_errors[index].total_mtr"
                          color="warning"
                        >
                          <template v-slot:activator="{ on, attrs }">
                            <span
                              icon
                              v-bind="attrs"
                              v-on="on"
                              style="display: inline;"
                            >
                              <v-icon small color="warning" class="mr-2">
                                mdi-alert-box
                              </v-icon>
                            </span>
                          </template>
                          <span style="display: inline;">{{error_simpan.data.validate_errors[index].total_mtr[0] }}</span>
                        </v-tooltip>
                        <input style="width: 70px;" type="text" v-model="item.total_mtr"/>
                      </div>
                    </td>              
                    <td>
                      <div style="display: inline-flex; align-items: center;">
                        <v-tooltip
                          top
                          v-if="error_simpan && error_simpan.data.validate_errors[index] && error_simpan.data.validate_errors[index].harga"
                          color="warning"
                        >
                          <template v-slot:activator="{ on, attrs }">
                            <span
                              icon
                              v-bind="attrs"
                              v-on="on"
                              style="display: inline;"
                            >
                              <v-icon small color="warning" class="mr-2">
                                mdi-alert-box
                              </v-icon>
                            </span>
                          </template>
                          <span style="display: inline;">{{error_simpan.data.validate_errors[index].harga[0] }}</span>
                        </v-tooltip>
                        <input @focus="ClearValue('harga',index)" @change="ConvertRp(index)" style="width: 100px;" type="text" v-model="item.harga_rp"/>                    
                      </div>
                    </td>              
                    <td>
                       <!-- <input style="width: 100px;" type="text" v-model="item.total"/>                     -->
                        {{ item.total_rp }}
                      </td>              
                  </tr>
                </tbody>
              </template>
            </v-simple-table>
            <v-divider></v-divider>
            <table class="ma-5">
              <tr>
                <td style="min-width: 150px;">Ongkos Kirim</td>
                <td>: {{ongkir | rupiah}}</td>
              </tr>
              <tr>
                <td>Total qty</td>
                <td>: {{sum_qty}} kg</td>
              </tr>
              <tr>
                <tr>
                  <td>Dpp</td>
                  <td>: {{dpp}}</td>
                </tr>
                <tr>
                  <td>Ppn</td>
                  <td>: {{ppn}}</td>
                </tr>
                <td>Total Contract</td>
                <td>: <span style="color: red;"> {{sum_total}}</span></td>
              </tr>
            </table>
            
            <v-overlay
                :absolute="false"
                :value="isAddingOngkir"
              >
              <v-card 
                
                light
              >
                <v-card-title>ongkos kirim: </v-card-title>
                <v-divider></v-divider>
                  <v-text-field
                  dense
                  class="ma-5"
                  label="ongkir"
                  filled
                  rounded
                  v-model="ongkir"
                  >
                  </v-text-field>
                <v-divider></v-divider>
                <v-card-actions class="d-flex justify-end">            
                  <v-btn
                    small
                    class=""
                    color="success"
                    @click="isAddingOngkir = false"
                  >
                    simpan
                  </v-btn>
                </v-card-actions>          
              </v-card>
            </v-overlay>
          </v-card>
          <div class="d-flex justify-end">
          <!-- <v-btn @click="exportToPDF()" class="error mt-5 mr-5"><v-icon>mdi-file-pdf-box</v-icon>generate</v-btn> -->
          <v-btn v-if="this.sc_id == ''" @click="simpan()" class="success mt-5"><v-icon>mdi-content-save-outline</v-icon>simpan</v-btn>
          <v-btn v-else @click="update()" class="success mt-5"><v-icon>mdi-content-save-outline</v-icon>Update</v-btn>
          </div>
        </div>
        <div v-else>
           <v-card outlined>
              <v-container class="d-flex justify-center"><span style="color:grey">tambahkan atau edit sales contract..</span></v-container>
           </v-card>
        </div>
       
        <v-overlay v-model="loading_open_form" :absolute="true">
          <v-progress-circular
            indeterminate
            size="40"
            ></v-progress-circular>
        </v-overlay>   
      </v-container>
    </v-card>

   
    <div :class="(preview_pdf == true) ? '' : 'd-none'" id="cetak2">
      <CetakPdf :form_sc_prop = "form_sc" :mode="mode"/>
    </div>
    <v-overlay v-model="loading_simpan">
      <v-progress-circular
        indeterminate
        size="64"
        v-if="berhasil_simpan == false && gagal_simpan == false"
      ></v-progress-circular>
      <v-sheet 
        class="d-flex rounded-circle justify-center"
        v-if="berhasil_simpan == true"
        color="success"
        height="100"
        width="100"
        >
        <v-icon
          size="64"
          color="white"
        >
          mdi-check-outline
        </v-icon>
      </v-sheet>
      <v-sheet 
        class="d-flex rounded-circle justify-center"
        v-if="gagal_simpan == true"
        color="error"
        height="100"
        width="100"
        >
        <v-icon
          size="64"
          color="white"
        >
          mdi-close-outline
        </v-icon>
      </v-sheet>
    </v-overlay>   
  </v-container>
</template>

<script>
// import html2pdf from "html2pdf.js";
import moment from 'moment';

export default {
  name: 'IndexPage',
  mounted() {
    this.html2pdf = require('html2pdf.js');
  // use html2pdf here
    this.get_sales_contract();
  },
  created(){
    this.date = this.$moment().format('YYYY-MM-DD');
  },
  data(){
    return {
       selected_sc : '',
       preview_pdf : false,
       mode: 'sc',
       moment : moment,
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
            value: 'nomor_sc',
        },
        {
            text: 'Nama Customoer',
            align: 'start',
            sortable: false,
            value: 'customer.name',
        },
        {
            text: 'Tanggal Contract',
            align: 'start',
            sortable: false,
            value: 'tanggal_sc',
        },
        {
            text: 'total contract',
            align: 'start',
            sortable: false,
            value: 'total',
        },
        {
            text: 'invoiced',
            align: 'center',
            sortable: false,
            value: 'invoiced',
        },
        { text: 'Actions', value: 'actions', sortable: false },
       ],
       sc_id : '',
       open_sc_form : false,
       loading_open_form: false,
       get_sales_contract_data: '',
       dialog_delete_sc: false,
       sc_id_todelete : '',
       items_sc :[],
       pagination: {
        page: 1, // Current page
        itemsPerPage: 5,
        itemsLength:0, // Number of items per page
       },
       search_sc : '',
       loading_sc: false,
       error_simpan : '',
       valid_customer: true,
       selected_customer: '',
       loading_customer:false,
       loading_simpan:false,
       berhasil_simpan : false,
       gagal_simpan : false,
       search_customer:'',
       html2pdf : '',
       isAddingOngkir : false,
       isAdding : true,
       sales_contract_no : 'SC/APS/XXX/bulan/tahun',
       date: '',
       date_sc: '',
       modal: false,
       modal2:false,
       list_customers : [ { att : {name:'test', id:1} }],
       customer : {
            nama :'',
            npwp :'',
            alamat:'',
            alamat_pengambilan:'',
            customer_id:'',
       },
       products: [
          {
            jenis_barang: '',
            code_coil: '',
            qty: 1,
            total_mtr:'',
            harga_rp: '',
            harga: '',
            total_rp: '',
            total: '',
          },          
        ],
      grand_total : 0,
      grand_total_rp : '0',
      grand_total_qty : 0,
      ongkir:0,
      sales_contract : ''
    } 
  },
  computed: {
      form_sc(){
        const form = {
           sc_id : this.sc_id,
           date : this.date,
           customer : this.customer,
           customer_json : JSON.stringify(this.customer),
           products : this.products,
           products_json : JSON.stringify(this.products),
           grand_total_rp : this.sum_total,
           grand_total_qty : this.sum_qty,
           grand_total: this.grand_total,
           ongkir : this.ongkir,
           sales_contract_no : this.sales_contract_no,
           customer_id : this.customer.customer_id,
        }
        return form;
     },
     dpp(){
      let result = this.grand_total / 1.11;
      return Intl.NumberFormat('id', { style: 'currency', currency: 'IDR' }).format(result);
      //  return this.grand_total / 1.11 ;
     },
     ppn(){
      let dpp = this.grand_total / 1.11;
      let result = (dpp * 11) / 100;
      return Intl.NumberFormat('id', { style: 'currency', currency: 'IDR' }).format(result);
     },
     sum_total(){
        this.grand_total = 0
        if(this.products.length > 0){
          this.products.forEach(item => {
            this.grand_total = Number(this.grand_total) + Number(item.total);
          })
        }
        this.grand_total = Number(this.grand_total) + Number(this.ongkir);
        this.grand_total_rp = Intl.NumberFormat('id', { style: 'currency', currency: 'IDR' }).format(this.grand_total)
        return Intl.NumberFormat('id', { style: 'currency', currency: 'IDR' }).format(this.grand_total)
     },
     sum_qty(){
        this.grand_total_qty = 0
        if(this.products.length > 0){
          this.products.forEach(item => {
            this.grand_total_qty = Number(this.grand_total_qty) + Number(item.qty);
          })
        }
         return this.grand_total_qty
     }
  },
  watch :{
     search_customer(value){
        // this.getCustomers(value);
        value && value !== this.selected_customer.name && value.length % 3 === 0 && this.getCustomers(value);
     },
     selected_customer(value){
        if (value === null){
          this.selected_customer = '';
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
  methods: {
      
      convert_rupiah(value){
        return Intl.NumberFormat('id', { style: 'currency', currency: 'IDR' }).format(value)
      },
      clear_form(){
          this.date = this.$moment().format('YYYY-MM-DD');;
          this.customer = {
            nama :'',
            npwp :'',
            alamat:'',
            alamat_pengambilan:'',
            customer_id:'',
          };
          this.products = [],      
          this.grand_total = '';
          this.grand_total_qty = '';
          this.ongkir = 0;
          this.sales_contract_no = '';
          this.customer.customer_id = '';
          this.error_simpan = '';
      },
      async open_form_page(){
        this.clear_form();
        this.sc_id = '';
        this.loading_open_form = true;
        await this.wait(1000);
        this.open_sc_form = true;
        this.loading_open_form = false
        this.$vuetify.goTo('#tambah_sc')
      },
      async close_form_page(){
        this.sc_id = '';
        this.loading_open_form = true;
        await this.wait(1000);
        this.open_sc_form = false;
        this.loading_open_form = false
        this.clear_form();
      },
      show_dialog_delete(id){
        this.dialog_delete_sc = true;
        this.sc_id_todelete = id;
      },
      close_dialog_delete(){
        this.dialog_delete_sc = false;
      },
      delete_sales_contract(){
        // console.log(item.id);
        this.dialog_delete_sc = false
        this.loading_simpan = true;
        this.loading_sc = true;
        this.pagination.page = 0;
        this.get_sales_contract_data = '';
        this.items_sc = [];
        this.$axios.delete('sales_contract/'+this.sc_id_todelete)
        .then( async response => {
          // console.log(response);
          // this.get_sales_contract();
          await this.wait(2000);
          this.loading_simpan = false;
          this.loading_sc = false;
        })
        .catch(error => {
          console.log(error);
          this.loading_sc = false;
            })
      },
      handlePagination(pagination) {
        if(this.pagination.page != pagination.page){
           this.loading_sc = true;
           this.pagination.page = pagination.page;
           this.get_sales_contract_data = '';
           this.items_sc = [];
           console.log(pagination)
           this.$axios.get('sales_contract?page='+pagination.page)
           .then(response => {
              // console.log(response);
              response.data.data.data.forEach((x ,index) => {
                  if(index == 0){
                    x.no = response.data.data.from ;
                  }else{
                    x.no = response.data.data.from + index ;
                  }
                  this.items_sc.push(x);
              })
              this.get_sales_contract_data =  response.data;
              this.pagination.itemsLength = response.data.data.total
              this.loading_sc = false;
            })
            .catch(error => {
              console.log(error);
              this.loading_sc = false;
            })
         }
      },
      get_sales_contract() {
          // let addno = [];
          this.loading_sc = true;
          this.$axios.get('/sales_contract')
          .then(response => {
            // console.log(response);
            response.data.data.data.forEach((x ,index) => {
                if(index == 0){
                  x.no = response.data.data.from ;
                }else{
                  x.no = response.data.data.from + index ;
                }
                this.items_sc.push(x);
            })
            this.get_sales_contract_data =  response.data;
            this.pagination.itemsLength = response.data.data.total
            this.loading_sc = false;
          })
          .catch(error => {
            console.log(error);
            this.loading_sc = false;
          })
      },  
      async preview_func(id){
        if (this.selected_sc == id){
          this.preview_pdf = false
          this.selected_sc = ''
        }else{
          this.preview_pdf = true;
          await this.wait(1000);
          this.$vuetify.goTo('#cetak2')
          this.selected_sc = id;
          this.show_sales_contract(id, 'export');
        }
      },
      show_sales_contract(id , action) {
        return new Promise( resolve => {
          this.clear_form();
          if (action == 'edit'){
            this.open_sc_form = true;
            this.loading_open_form = true;
            this.$vuetify.goTo('#tambah_sc')
          }
          this.$axios.get('/sales_contract/'+id)
          .then(response => {
            // console.log(response.data.data);
            var data = response.data.data;
            this.sc_id = data.id;
            this.date = data.tanggal_sc;
            this.customer = {
              nama : data.customer.name,
              npwp :   data.customer.npwp,
              alamat:  data.customer.alamat,
              alamat_pengambilan:  data.alamat_pengambilan,
              customer_id:  data.customer.id
            };
            //insert data product
            data.item.forEach(x => {
               x.harga_rp = this.convert_rupiah(x.harga);
               x.total = x.qty * x.harga;
               x.total_rp = this.convert_rupiah(x.total);
               this.products.push(x);
            });
            this.ongkir = data.ongkir;
            this.sales_contract_no = data.nomor_sc;
            this.customer.customer_id = data.customer.id;
            this.loading_open_form = false;
            resolve(true);
          })
          .catch(error => {
            console.log(error);
            this.loading_sc = false;
            resolve(false);
          })
        });
      },
        
      search_sales_contract() {
          // let addno = [];
          this.loading_sc = true;
          this.pagination.page = 1;
          this.get_sales_contract_data = '';
          this.items_sc = [];

          this.$axios.post('/sales_contract/search-sc', {search_sc : this.search_sc , date_sc : this.date_sc})
          .then(response => {
            // console.log(response);
            response.data.data.data.forEach((x ,index) => {
                if(index == 0){
                  x.no = response.data.data.from ;
                }else{
                  x.no = response.data.data.from + index ;
                }
                this.items_sc.push(x);
            })
            this.get_sales_contract_data =  response.data;
            this.pagination.itemsLength = response.data.data.total
            this.loading_sc = false;
          })
          .catch(error => {
            console.log(error);
            this.loading_sc = false;
          })
      },  
      validate_customer () {
        this.$refs.form.validate()
      },
      updateCustomer(){
        if(this.selected_customer){
          this.customer.nama = this.selected_customer.nama;
          this.customer.alamat_pengambilan = this.selected_customer.alamat;
          this.customer.customer_id = this.selected_customer.id;
          this.customer.alamat = this.selected_customer.alamat;
          this.customer.npwp = this.selected_customer.npwp;
        }
      },
      getCustomers(value){
        this.loading_customer = true;
         this.$axios.post('/customers/search-customer', {name : value}) 
         .then(response => {
              this.list_customers = response.data.data.map(item => {
                 return { att : {npwp : item.npwp, nama: item.name, name : item.name.toUpperCase(), alamat : item.alamat.toUpperCase(), id : item.id}}
              });
              this.loading_customer = false;
         })
         .catch(error => {
              console.log(error); 
              this.loading_customer = false;
         });
      },

      wait(ms) {
        return new Promise(resolve => {
          setTimeout(resolve, ms);
        });
      },
      async simpan(){
         await this.validate_customer();

         if (this.valid_customer == true){
           this.loading_simpan = true;
           this.sales_contract = this.$axios.post('/sales_contract', this.form_sc)
           .then(async response => {
                // console.log(response);
                this.berhasil_simpan = true;
                await this.wait(5000);
                this.loading_simpan = false;
                this.berhasil_simpan = false;
                this.close_form_page();
                this.search_sales_contract();
           })
           .catch(async error => {
                console.log(error.response.status);
                this.gagal_simpan = true;
                this.error_simpan = error.response;
                await this.wait(5000); 
                this.loading_simpan = false;
                this.gagal_simpan = false;
                // this.close_form_page();
                // this.search_sales_contract();
           });
         } else {
           this.$vuetify.goTo('#form_customer')
         }
      },
      async update(){
         await this.validate_customer();

         if (this.valid_customer == true){
           this.loading_simpan = true;
           this.sales_contract = this.$axios.post('/sales_contract/update-sc/'+this.sc_id, this.form_sc)
           .then(async response => {
                // console.log(response);
                this.berhasil_simpan = true;
                await this.wait(5000);
                this.loading_simpan = false;
                this.berhasil_simpan = false;
                this.close_form_page();
                this.search_sales_contract();
           })
           .catch(async error => {

                console.log(error.response.status);
                this.gagal_simpan = true;
                this.error_simpan = error.response;
                await this.wait(5000); 
                this.loading_simpan = false;
                this.gagal_simpan = false;
                // this.close_form_page();
                // this.search_sales_contract();
           });
         } else {
           this.$vuetify.goTo('#form_customer')
         }
      },
      async exportToPDF(id) {
        this.close_form_page();
        this.clear_form();
        let fetch_sc = await this.show_sales_contract(id, 'export');
        if(fetch_sc){
          console.log('telah fetch')
          const options = {
            margin: [5, 5, 5, 5], // Set the margins of the PDF
            filename: this.customer.nama+'_'+this.$moment().format('YYYY-MM-DD'), // Set the name of the PDF file
            image: { type: 'jpeg', quality: 2 }, // Set the image quality of the PDF
            html2canvas: { scale: 4 }, // Set the scale of the PDF
            jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' }, // Set the format and orientation of the PDF
          };
  
          const element = document.getElementById("cetak2");
          
          console.log('Running on a web');
          this.html2pdf().set(options).from(element).save();
          // do something for any other platform
        }
      },
      async exportToPDF_api(id) {
        this.close_form_page();
        this.clear_form();
        let fetch_sc = await this.show_sales_contract(id, 'export');
        if(fetch_sc){
          this.$axios.post('/download-pdf',{id : id, tipe : 'sc'},{ responseType: 'blob' })
              .then(response => {                 
                  // Create a Blob object from the response data
                  const blob = new Blob([response.data], { type: 'application/pdf' });
                  // Create a temporary URL for the Blob
                  const url = window.URL.createObjectURL(blob);
                  // Create a link element and simulate a click to trigger the download
                  const link = document.createElement('a');
                  link.href = url;
                  link.setAttribute('download', 'sales_contract.pdf'); // Set the filename
                  document.body.appendChild(link);
                  link.click();
                  // Cleanup
                  window.URL.revokeObjectURL(url);
              })
              .catch(error => {
                  console.log(error);
                  this.loading_rekap = false;
              })
        }
      },
      AddProduct(){
         this.products.push(
            {
              jenis_barang: '',
              code_coil: '',
              qty: 1,
              total_mtr:'',
              harga_rp: '',
              harga:'',
              total_rp: '',
              total: '',
            }, 
         )
      },
      RmProduct(index){
        // 
        this.products.splice(index,1);
      },
      CopyValue(column){

        if (column == 'jenis_barang'){
          this.products.forEach(item => {
            item.jenis_barang = this.products[0].jenis_barang; 
          })
        }
        if (column == 'code_coil'){
          this.products.forEach(item => {
            item.code_coil = this.products[0].code_coil; 
          })
        }
        if (column == 'qty'){
          this.products.forEach((item,index) => {
            item.qty = this.products[0].qty; 
            this.HitungTotal(index)
          })
        }
        if (column == 'total_mtr'){
          this.products.forEach((item) => {
            item.total_mtr = this.products[0].total_mtr; 
          })
        }
        if (column == 'harga'){
          this.products.forEach((item,index) => {
            item.harga = this.products[0].harga; 
            item.harga_rp = Intl.NumberFormat('id', { style: 'currency', currency: 'IDR' }).format(this.products[0].harga);
            this.HitungTotal(index)
          })
        }
      },
      ConvertRp(index){
        this.products[index].harga = this.products[index].harga_rp;
        this.products[index].harga_rp = Intl.NumberFormat('id', { style: 'currency', currency: 'IDR' }).format(this.products[index].harga_rp);
        this.HitungTotal(index);
      },
      HitungTotal(index){
        const rupiah = this.products[index].total = this.products[index].qty * this.products[index].harga;
        this.products[index].total_rp =  Intl.NumberFormat('id', { style: 'currency', currency: 'IDR' }).format(rupiah);
      },
      ClearValue(column, index){
        if(column == 'harga'){
          this.products[index].harga_rp = '';
          this.products[index].total_rp = '';
          this.products[index].total = '';
        }
        if(column == 'total'){
          this.products[index].total_rp = '';
          this.products[index].total = '';
        }
      }
  }
}
</script>

<!-- <style>
  #table-products table   {
    border-collapse: collapse;
  }

   #table-products table td, #table-products table  th {
    border: 1px solid black;
    padding: 8px;
  }
  .v-application--is-ltr .v-data-footer__pagination {
  margin: 0 32px 0 18px;
  }
  .v-data-footer {
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    font-size: 0.75rem;
    padding: 0 8px;
    --justify-content: center;
  }
</style> -->
