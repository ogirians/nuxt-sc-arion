<template>
  <v-container>
    <v-card class="logo" color="primary">
        <v-card-title class="pt-5 pt-md-0 pb-md-0">
            <h3 style="color:white" class="mr-5"> 
              sales contract
            </h3>
            <v-text-field
              class="mt-3"
              dark
              value="SC/APS/XXX/bulan/tahun"
            >
                
            </v-text-field>          
        </v-card-title>
    </v-card>      
    <v-card class="logo py-4">   
      <v-container>
        <v-row class="mt-5">
          <v-col class="py-0" cols="12">
            <v-autocomplete
              label = "cari nama customer"
              clearable
              filled
              v-if="isAdding == false"
              dense
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
            </v-autocomplete>
            <v-text-field
              label="nama customer baru"
              placeholder="nama"
              filled
              v-if ="isAdding == true"
              dense
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
          <v-col class="py-0" cols="12" md="6">            
            <v-text-field
              label="npwp"              
              filled              
              dense
            >              
            </v-text-field>                   
          </v-col>
          <v-col class="py-0" cols="12" md="6">            
            <v-text-field
              label="alamat"              
              filled    
              small         
              dense 
            >              
            </v-text-field>                   
          </v-col>
        </v-row>
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
        List barang
        <v-spacer></v-spacer>
        <v-btn small rounded color="success" class="mr-3" @click="AddProduct()">
          <v-icon>mdi-plus</v-icon>
          item
        </v-btn>
        <v-btn small rounded color="secondary" @click="isAddingOngkir = true">
          <v-icon>mdi-plus</v-icon>
          ongkir
        </v-btn>
      </v-card-title>
       
    </v-card>
    <v-card class="logo">
      <v-simple-table        
        
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
              <td class style="min-width: 70px;">
                <span style="display: inline;">{{ index + 1 }}<v-icon @click="RmProduct(index)" small color="error">mdi-minus-circle</v-icon></span>                
              </td>              
              <td>
                 <input style="width: 300px;" type="text" v-model="item.jenis_barang"/>                    
              </td>              
              <td>
                 <input style="width: 100px;" type="text" v-model="item.code_coil"/>                    
              </td>              
              <td>
                 <input @change="HitungTotal(index)" style="width: 70px;" type="number" v-model="item.qty"/>                    
              </td>              
              <td>
                 <input style="width: 70px;" type="text" v-model="item.total_mtr"/>                    
              </td>              
              <td>
                 <input @focus="ClearValue('harga',index)" @change="ConvertRp(index)" style="width: 100px;" type="text" v-model="item.harga_rp"/>                    
              </td>              
              <td>
                 <!-- <input style="width: 100px;" type="text" v-model="item.total"/>                     -->
                  {{ item.total_rp }}
                </td>              
            </tr>
          </tbody>
        </template>
      </v-simple-table>
      <v-card-actions class="ml-5 mt-5 pb-0">
         <h4>total contract : <span style="color:red">{{ grand_total_rp }}</span> </h4>
        </v-card-actions>
      <v-card-actions class="ml-5 pt-0">
        <h5 class="mb-5">total qty : <span>{{ grand_total_qty }} kg</span> </h5>
      </v-card-actions>
      <v-overlay
          :absolute="true"
          :value="isAddingOngkir"
        >
          <v-btn
            color="success"
            @click="isAddingOngkir = false"
          >
            Hide Overlay
          </v-btn>
        </v-overlay>
    </v-card>
  </v-container>   
</template>

<script>
export default {
  name: 'IndexPage',
  data(){
    return {
       isAddingOngkir : false,
       isAdding : true,
       date:'2023-06-13',
       modal: false,
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
      grand_total_rp : '',
      grand_total_qty : 0,
      ongkir:0
    } 
  },
  computed: {
     sum_total(){
        this.grand_total = 0
        if(this.products.length > 0){
          this.products.forEach(item => {
            this.grand_total = Number(this.grand_total) + Number(item.total);
          })
        }
        this.grand_total_rp = Intl.NumberFormat('id', { style: 'currency', currency: 'IDR' }).format(this.grand_total)
        return this.grand_total
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

  },
  methods: {
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
