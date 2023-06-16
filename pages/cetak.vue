<template>
    <div>
        <div style="text-align: center; margin: auto; width: 75%;">
            <b>CV. ARION PANCA SEKAWAN</b><br>
            <b>
                PEKARUNGAN RT.011/004 PEKARUNGAN SUKODONO - SIDOARJO<br>
            </b>
            <b>
                Email: arionpancasekawan@gmail.com<br>
            </b>
            <b>
                Web : www.arionpancasekawan.com
            </b>
        </div>
        <br>
        <v-divider></v-divider>
        <br>
        
        <div style="text-align: center; margin: auto; width: 75%;">
               <b>SALES CONTRACT</b>
        </div>
        <div style="text-align: center; margin: auto; width: 75%;">
               SC/APS/0002/06/2023                          
        </div>
        <v-row class="mt-5">
            <v-col cols="6" >
                TANGGAL : 
            </v-col>
            <v-col cols="6">
                ALAMAT PENGAMBILAN :
            </v-col>
            <v-col cols="12" class="pt-0">
                CUSTOMER :
            </v-col>
            <v-col cols="12" class="pt-0">
                NPWP :
            </v-col>
            <v-col cols="12" class="pt-0">
                ALAMAT :
            </v-col>
        </v-row>
        <div class="d-flex justify-center mt-8 mb-8">
            <table id="table-cetak">
                <thead>
                <tr>
                  <th class="text-left">
                    NO
                  </th>
                  <th class="text-left">
                    JENIS BARANG<br>                
                  </th>
                  <th class="text-left">
                    CODE COIL<br>               
                  </th>
                  <th class="text-left">
                    QTY (Kg)<br>                
                  </th>
                  <th class="text-left">
                    TOTAL (Mtr)<br>                
                  </th>
                  <th class="text-left">
                    HARGA<br>                
                  </th>
                  <th class="text-left">
                    TOTAL
                  </th>
                </tr>
                </thead>
                <tbody>

                </tbody>
            </table>
        </div>
        
        <v-row>
            <v-col cols="12">
                CONTRACT CONDITION :
            </v-col>
            <v-col cols="1" class = "pb-0">
                I 
            </v-col>
            <v-col cols="11" class = "pb-0">
                Payment term
            </v-col>
            <v-col cols="1" class="pt-0 pb-0">                 
            </v-col>
            <v-col cols="11" class="pt-0 pb-0">
                <b>CBD (CASH BEFORE DELIVERY)</b>
            </v-col>
            <v-col cols="1" class="pt-0 pb-0">                 
                II
            </v-col>
            <v-col cols="11" class="pt-0 pb-0">                 
                Harga LOCO gudang
            </v-col>
            <v-col cols="1" class="pt-0 pb-0">                 
                III
            </v-col>
            <v-col cols="11" class="pt-0 pb-0">
                Pembayaran
            </v-col>
            <v-col cols="1" class="pt-0 pb-0">                                 
            </v-col>
            <v-col cols="11" class="pt-0 pb-0">                 
                <b>Pembayaran DIANGGAP SAH JIKA DANA SUDAH MASUK KE REKENING</b>
            </v-col>
            <v-col cols="1" class="pt-0 pb-0">                                 
            </v-col>
            <v-col cols="11" class="pt-0 pb-0">                 
                <b>BANK BCA </b>
            </v-col>
            <v-col cols="1" class="pt-0 pb-0">                                 
            </v-col>
            <v-col cols="11" class="pt-0 pb-0">                 
                <b>AC : 0877170822</b>
            </v-col>
            <v-col cols="1" class="pt-0 pb-0">                                 
            </v-col>
            <v-col cols="11" class="pt-0 pb-0">                 
                <b>AN : ARION PANCA SEKAWAN .CV</b>
            </v-col>
            <v-col cols="1" class="pt-0 pb-0">                                 
                IV
            </v-col>
            <v-col cols="11" class="pt-0 pb-0">                 
                Waktu pengambilan barang
            </v-col>
            <v-col cols="1" class="pt-0 pb-0">                                 
                
            </v-col>
            <v-col cols="11" class="pt-0 pb-0">                 
                Realisasi pengambilan barang mohon info H-1 sebelum tanggal pengambilan
             </v-col>
             <v-col cols="1" class="pt-0 pb-0">                                 
                V
            </v-col>
            <v-col cols="11" class="pt-0 pb-0">                 
                Jika Sales Contract disetujui, mohon ditandatangani dan dikirim kembali
            </v-col>
            <v-col cols="1" class="pt-0 pb-0">                                 
                Note
            </v-col>
            <v-col cols="11" class="pt-0 pb-0">                 
                Harap SC ini diperiksa terlebih dahulu, bila terjadi kesalahan dalam pemesanan & SC udah di ACC pihak customer maka pihak CV.ARION PANCA SEKWAN tidak bertanggung jawab.
            </v-col>
            <v-col cols="6" class="mt-8 d-flex justify-center"> 
                hormat saya                                 
            </v-col>
            <v-col cols="6" class="mt-8 d-flex justify-center">                 
                Disetujui oleh
            </v-col>
            <v-col cols="6" class="mt-12 d-flex justify-center"> 
                CV. ARION PANCA SEKAWAN                                 
            </v-col>
            <v-col cols="6" class="mt-12 d-flex justify-center">                 
                Customer
            </v-col>
        </v-row>
    </div>
</template>

<script>
export default{
    props: {
        form_sc_prop : ''
    },
    data() {
        return {
            form : this.form_sc_prop
        }
    }
}

</script>

<style>
  table {
    border-collapse: collapse;
  }

  td, th {
    border: 1px solid black;
    padding: 8px;
  }
</style>