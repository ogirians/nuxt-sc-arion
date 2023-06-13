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
        <v-btn small rounded color="success" @click="AddProduct()">
          <v-icon>mdi-plus</v-icon>
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
                JENIS BARANG
              </th>
              <th class="text-left">
                CODE COIL
              </th>
              <th class="text-left">
                QTY (Kg)
              </th>
              <th class="text-left">
                TOTAL (Mtr)
              </th>
              <th class="text-left">
                HARGA
              </th>
              <th class="text-left">
                TOTAL
              </th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="item in products"              
            >
              <td>
                 <input style="width: 20px;" type="text" v-model="item.no"/>                    
              </td>              
              <td>
                 <input style="width: 300px;" type="text" v-model="item.jenis_barang"/>                    
              </td>              
              <td>
                 <input style="width: 100px;" type="text" v-model="item.code_coil"/>                    
              </td>              
              <td>
                 <input style="width: 70px;" type="text" v-model="item.qty"/>                    
              </td>              
              <td>
                 <input style="width: 70px;" type="text" v-model="item.total_mtr"/>                    
              </td>              
              <td>
                 <input style="width: 70px;" type="text" v-model="item.harga"/>                    
              </td>              
              <td>
                 <input style="width: 100px;" type="text" v-model="item.total"/>                    
              </td>              
            </tr>
          </tbody>
        </template>
      </v-simple-table>
    </v-card>
  </v-container>   
</template>

<script>
export default {
  name: 'IndexPage',
  data(){
    return {
       isAdding : true,
       date:'2023-06-13',
       modal: false,
       products: [
          {
            no : '1',
            jenis_barang: '',
            code_coil: '',
            qty:'',
            total_mtr:'',
            harga:'',
            total:'',
          },          
        ],
    } 
  },
  methods: {
      AddProduct(){
         this.products.push(
            {
              no : '2',
              jenis_barang: '',
              code_coil: '',
              qty:'',
              total_mtr:'',
              harga:'',
              total:'',
            }, 
         )
      }
  }
}
</script>
