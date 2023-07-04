<template>
  <v-container>
    <v-card class="logo" color="primary">
        <v-card-title class="pt-5 pt-md-0 pb-md-0">
            <span style="color:white" class="mr-5"> 
              sales contract
            </span>
            <v-text-field
              class="mt-3"
              dark
              v-model="sales_contract_no"
            >
                
            </v-text-field>          
        </v-card-title>
    </v-card>      
    <v-card class="logo py-4">   
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
    <v-card color="primary" dark>
       <v-card-title>
        <span>List barang</span>
        <v-spacer></v-spacer>
        <v-btn small rounded color="success" class="mr-3" @click="AddProduct()">
          <v-icon small>mdi-plus</v-icon>
          <span v-if="$vuetify.breakpoint.name == 'md'">item</span>
        </v-btn>
        <v-btn small rounded color="secondary" @click="isAddingOngkir = true">
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
    <v-card class="logo">
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
              <td class style="min-width: 50px;">
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
      <v-card-actions class="ml-5 mt-5 pb-0">
         <h4>total contract : <span style="color:red">{{ sum_total }}</span> </h4>
        </v-card-actions>
      <v-card-actions class="ml-5 pt-0 pb-0">
        <h5 class="">ongkos kirim : <span>{{ ongkir | rupiah}}</span> </h5>
      </v-card-actions>
      <v-card-actions class="ml-5 pt-0">
        <h5 class="mb-5">total qty : <span>{{ sum_qty }} kg</span> </h5>
      </v-card-actions>
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
      <v-btn @click="exportToPDF()" class="error mt-5 mr-5"><v-icon>mdi-file-pdf-box</v-icon>generate</v-btn>
      <v-btn @click="simpan()" class="error mt-5"><v-icon>mdi-content-save-outline</v-icon>simpan (soon)</v-btn>
    </div>
    <div class="d-none" id="cetak2">
      <CetakPdf :form_sc_prop = "form_sc" />
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

export default {
  name: 'IndexPage',
  mounted() {
    this.html2pdf = require('html2pdf.js');
  // use html2pdf here
  },
  created(){
    this.date = this.$moment().format('YYYY-MM-DD');;
  },
  data(){
    return {
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
       modal: false,
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
    }
  },
  methods: {
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
                console.log(response);
                this.berhasil_simpan = true;
                await this.wait(5000);
                this.loading_simpan = false;
                this.berhasil_simpan = false;
           })
           .catch(async error => {
                console.log(error.response.status);
                this.gagal_simpan = true;
                this.error_simpan = error.response;
                await this.wait(5000); 
                this.loading_simpan = false;
                this.gagal_simpan = false;
           });
         } else {
           this.$vuetify.goTo('#form_customer')
         }
      },

      async exportToPDF() {
        const options = {
          margin: [5, 5, 5, 5], // Set the margins of the PDF
          filename: 'my-document.pdf', // Set the name of the PDF file
          image: { type: 'jpeg', quality: 2 }, // Set the image quality of the PDF
          html2canvas: { scale: 4 }, // Set the scale of the PDF
          jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' }, // Set the format and orientation of the PDF
        };

        const element = document.getElementById("cetak2");
        
        console.log('Running on a web');
        this.html2pdf().set(options).from(element).save();
        // do something for any other platform

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

<style>
  #table-products table   {
    border-collapse: collapse;
  }

   #table-products table td, #table-products table  th {
    border: 1px solid black;
    padding: 8px;
  }
</style>
