<template>
    <div id="halaman_cetak">
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
               {{ form.sales_contract_no}}<br><br>
        </div>
        <div style="margin-top: 10px;">
                TANGGAL : {{ form.date }}<br>
                ALAMAT PENGAMBILAN : {{ form.customer.alamat_pengiriman }}<br>
                CUSTOMER : {{ form.customer.nama }}<br>
                NPWP : {{ form.customer.npwp }}<br>
                ALAMAT : {{ form.customer.alamat }}<br><br><br>
        </div>
        <div class="d-flex justify-center">
            <table id="cetak">
                <thead>
                <tr>
                    <th>
                    <b>NO</b>
                    </th>
                    <th>
                    <b>JENIS BARANG</b>
                    </th>
                    <th>
                    <b>CODE COIL</b>
                    </th>
                    <th>
                    <b>QTY (Kg)</b>
                    </th>
                    <th>
                    <b>TOTAL (Mtr)</b>
                    </th>
                    <th>
                    <b>HARGA</b>
                    </th>
                    <th>
                    <b>TOTAL</b>
                    </th>
                </tr>
                </thead>
                <tbody>
                    <tr
                        v-for="(item, index) in form.products" :key = "index"      
                        >
                        <td>
                            <span style="display: inline;">{{ index + 1 }}</span>                
                        </td>              
                        <td>
                            {{ item.jenis_barang }}
                        </td>              
                        <td>
                            {{ item.code_coil }}
                        </td>              
                        <td>
                            {{ item.qty }}
                        </td>              
                        <td>
                            {{ item.total_mtr }}
                        </td>              
                        <td>
                            {{ item.harga }}
                        </td>              
                        <td>
                            {{ item.total_rp }}
                        </td>              
                    </tr>
                </tbody>
            </table>
        </div>
        <br>
        <br>
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
  #halaman_cetak {
    font-size: 14px;
  }

  table#cetak   {
    border-collapse: collapse;
    margin: auto;
    table-layout: fixed;
    width: 100%;
  }

  table#cetak td ,  table#cetak th {
    border: 1px solid black;
    padding: 2px;
    /* width: 50%; */
    word-wrap: break-word;
    /* white-space: pre-wrap; */
    /* font-weight: bold; */
  }
</style>