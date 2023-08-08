<template>
    <div id="halaman_cetak">
       
        <table style="text-align: center; margin: auto; width: 100%;" >
            <tr>
                <td style="width: 20%;"> 
                    <img
                    src="/arion.jpg"
                    alt="arion"
                    class="mt-5"
                    height="100px"
                    
                    > 
                </td>
                <td style="width: 60%;">
                    <div>
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
                </td>
                <td style="width: 20%;"></td>
            </tr>
        </table>
        
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
            <div style="display:flex">
                <table style="width: 70%;">
                    <tr>
                        <td style="margin-right : 10px;">TANGGAL</td>
                        <td>&nbsp;&nbsp;:&nbsp;</td>
                        <td style="min-width: 80%;">{{ form.date | tanggal_id }}</td>
                        <!-- <td style="margin-right : 10px">ALAMAT PENGAMBILAN</td>
                        <td>&nbsp;&nbsp;:&nbsp;</td>
                        <td>{{ form.customer.alamat_pengambilan }}</td> -->
                    </tr>
                    <tr>
                        <td style="margin-right : 10px"> CUSTOMER</td>
                        <td>&nbsp;&nbsp;:&nbsp;</td>
                        <td>{{ form.customer.nama }}</td>
                    </tr>
                    <tr>
                        <td style="margin-right : 10px">NPWP</td>
                        <td>&nbsp;&nbsp;:&nbsp;</td>
                        <td>{{ form.customer.npwp }}</td>
                    </tr>
                    <tr>
                        <td style="margin-right : 10px">ALAMAT</td>
                        <td>&nbsp;&nbsp;:&nbsp;</td>
                        <td>{{ form.customer.alamat }}</td>
                    </tr>
                </table>
                <table style="width: 30%;">
                    <tr style="height: 20%;">
                        <td style="margin-right : 10px; vertical-align:top;">ALAMAT PENGAMBILAN :</td>
                        <!-- <td>&nbsp;&nbsp;:&nbsp;</td> -->
                        
                    </tr>
                    <tr>
                        <td style="vertical-align:top; word-wrap: break-word;">{{ form.customer.alamat_pengambilan }}</td>
                    </tr>
                </table>
            </div>
                <!-- TANGGAL : {{ form.date }}<br>
                ALAMAT PENGAMBILAN : {{ form.customer.alamat_pengiriman }}<br>
                CUSTOMER : {{ form.customer.nama }}<br>
                NPWP : {{ form.customer.npwp }}<br>
                ALAMAT : {{ form.customer.alamat }} -->
                <br><br><br>
        </div>
        <div class="d-flex justify-center">
            <table id="cetak">
                <thead>
                <tr>
                    <th style="max-width: 20px;">
                    <b>NO</b>
                    </th>
                    <th style="max-width: 250px;">
                    <b>JENIS BARANG</b>
                    </th>
                    <th style="max-width: 100px;">
                    <b>CODE COIL</b>
                    </th>
                    <th style="max-width: 100px;">
                    <b>QTY (Kg)</b>
                    </th>
                    <th style="max-width: 100px;">
                    <b>TOTAL (Mtr)</b>
                    </th>
                    <th style="max-width: 100px;">
                    <b>HARGA</b>
                    </th>
                    <th style="max-width: 150px;">
                    <b>TOTAL</b>
                    </th>
                </tr>
                </thead>
                <tbody>
                    <tr
                        v-for="(item, index) in form.products" :key = "index"      
                        >
                        <td style="max-width: 20px;">
                            <span style="display: inline;">{{ index + 1 }}</span>                
                        </td>              
                        <td style="max-width: 250px;">
                            {{ item.jenis_barang }}
                        </td>              
                        <td style="max-width: 100px;">
                            {{ item.code_coil }}
                        </td>              
                        <td style="max-width: 100px;">
                            {{ item.qty }}
                        </td>              
                        <td style="max-width: 100px;">
                            {{ item.total_mtr }}
                        </td>              
                        <td style="max-width: 100px;">
                            {{ item.harga_rp }}
                        </td>              
                        <td style="max-width: 150px;">
                            {{ item.total_rp }}
                        </td>              
                    </tr>
                    <tr>
                        <td></td>
                        <td colspan="4" style="text-align: center">ONGKIR</td>
                        <td>{{ form.ongkir | rupiah }}</td>
                        <td>{{ form.ongkir | rupiah }}</td>
                    </tr>
                    <tr>
                        <td colspan="3" style="text-align: center"><b>TOTAL</b></td>
                        <td>{{ form.grand_total_qty }}</td>
                        <td></td>
                        <td></td>
                        <td>{{ form.grand_total_rp }}</td>
                    </tr>
                </tbody>
            </table>
        </div>
        <br>
        <br>
        <table id="condition">
            <tr>
                <td colspan="2">CONTRACT CONDITION :</td>
            </tr>
            <tr>
                <td>I</td>
                <td>Payment term</td>
            </tr> 
            <tr>
                <td></td>
                <td><b>CBD (CASH BEFORE DELIVERY)</b></td>
            </tr> 
            <tr>
                <td>II</td>
                <td>Harga LOCO gudang</td>
            </tr>   
            <tr>
                <td>III</td>
                <td>Pembayaran</td>
            </tr>  
            <tr>
                <td></td>
                <td>Pembayaran DIANGGAP SAH JIKA DANA SUDAH MASUK KE REKENING</td>
            </tr>  
            <tr>
                <td></td>
                <td>Biaya transfer perbankan sepenuhnya tanggung jawab customer</td>
            </tr>  
            <tr>
                <td></td>
                <td><b>BANK BCA </b></td>
            </tr> 
            <tr>
                <td></td>
                <td><b>AC : 0877170822</b></td>
            </tr> 
            <tr>
                <td></td>
                <td><b>AN : ARION PANCA SEKAWAN .CV</b></td>
            </tr> 
            <tr>
                <td>IV</td>
                <td>Waktu pengambilan barang</td>
            </tr>  
            <tr>
                <td></td>
                <td>Realisasi pengambilan barang mohon info H-1 sebelum tanggal pengambilan</td>
            </tr> 
            <tr>
                <td>V</td>
                <td> Jika Sales Contract disetujui, mohon ditandatangani dan dikirim kembali</td>
            </tr>  
            <tr>
                <td>VI</td>
                <td>Barang yang tidak diambil lebih dari 2 minggu setelah tanggal kontrak akan dikenakan biaya gudang</td>
            </tr>  
            <tr>
                <td>VII</td>
                <td>Truk pengambilan coil harus sesuai dengan standar keamanan</td>
            </tr>  
            <tr>
                <td>VIII</td>
                <td>Mohon membawa palet demi keamanan coil</td>
            </tr>  
            <tr>
                <td>Note</td>
                <td>Harap SC ini diperiksa terlebih dahulu, bila terjadi kesalahan dalam pemesanan & SC udah di ACC pihak customer maka pihak CV.ARION PANCA SEKWAN tidak bertanggung jawab.</td>
            </tr>  
        </table>
        <br>
        <table id="ttd">
            <tr>
                <td>Hormat Kami</td>
                <td>Disetujui Oleh</td>
            </tr>
            <tr>
                <td>
                    <div style="position: relative; display: inline-block;">
                        <img src="/ttd_arion.png" height="60x" style="position: absolute; z-index: 2;  top: 50%; left: 10%; transform: translate(-50%, -50%);">
                        <img src="/stamp_arion.png" height="120px">
                    </div>
                </td>
                <td></td>
            </tr>
            <tr>
                <td>CV. ARION PANCA SEKAWAN</td>
                <td>Customer</td>
            </tr>
        </table>
    </div>
</template>

<script>
import moment from 'moment';
export default{
    props: {
        form_sc_prop : ''
    },
    data() {
        return {
            // form : this.form_sc_prop
        }
    },
    computed : {
        form(){
            return this.form_sc_prop;
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

<style>
  #halaman_cetak {
    font-size: 14px;
  }

  table#cetak   {
    border-collapse: collapse;
    margin: auto;
    /* table-layout: fixed; */
    width: 100%;
    text-align: center;
  }

  table#cetak td ,  table#cetak th {
    border: 1px solid black;
    padding: 2px;
    /* width: 50%; */
    word-wrap: break-word;
    /* white-space: pre-wrap; */
    /* font-weight: bold; */
    /* max-width: 500px; */
  }


  table#condition td {
      vertical-align: top;
      min-width: 50px;
      
    }
 
  table#ttd {
        width: 100%;
        /* border :1px solid black; */
        text-align: center;
        table-layout: fixed;
    }

  table#ttd td{
        max-width: 50%;      
        /* padding-top: 50px; */
    }
</style>