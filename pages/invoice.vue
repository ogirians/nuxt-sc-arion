<template>
    <v-container>
      <v-app-bar
        fixed
        app
        elevation="0"
        color="white"
        class="pa-0"
        :height="height"
        style="margin-top: 55px"
        :hide-on-scroll="hide_bar"
      > 
        <v-container> 
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
                  :return-value.sync="date_invoice_search"
                  persistent
                  width="290px"
                >
                  <template v-slot:activator="{ on, attrs2 }">
                    <v-text-field            
                      v-model="date_invoice_search"
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
                    v-model="date_invoice_search"
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
                      @click="$refs.dialog2.save(date_invoice_search)"
                    >
                      OK
                    </v-btn>
                  </v-date-picker>
                </v-dialog>
              </v-col>
              <v-col cols="12" md="4" class="py-0 mb-0">
                <v-btn @click="search_invoice_func()" elevation="0" color="info" class="ml-3 mb-3 mt-1">
                  cari
                </v-btn>
              </v-col>
          </v-row>
        </v-container>
      </v-app-bar>
      <v-alert
        dismissible
        type="success"
        v-if = "pushed_to_jurnal == true"
        v-model = "pushed_to_jurnal"
      >berhasil push data {{ selected_no_inv }} ke jurnal </v-alert>
      <v-alert
        dismissible
        type="error"
        v-if = "is_fail_to_jurnal == true"
        v-model ="is_fail_to_jurnal"
      >gagal push data {{ selected_no_inv }} ke jurnal </v-alert>
      <v-card class="logo" color="primary" elevation="5">
          <v-img 
              src="/card_background.jpg" 
              max-height="100"
              max-width="100%"
              style="position: absolute; top: 0px; right: 0px; opacity: 0.2; border-radius:10px">
          </v-img>
          <v-card-title>
              <span style="color:white; position: absolute; z-index: 1;" class="mr-5"> 
              RIWAYAT INVOICE
              </span>
              <v-spacer></v-spacer>
              <v-btn @click="isAddingInvoice = true" small rounded color="success" class="mr-3">
              <v-icon small>mdi-plus</v-icon>
              <span v-if="$vuetify.breakpoint.name == 'md'">sales contract</span>
              </v-btn>
              <v-btn @click="search_invoice_func(pagination.page)" small rounded color="primary" class="mr-3">
                <v-icon small>mdi-refresh</v-icon>
                Refresh
              </v-btn>
          </v-card-title>
      </v-card>
      <v-card class="logo mb-8" elevation="5">   
        <v-container>
          <v-card outlined>
            <v-data-table
              :headers="headers_sc"
              :items="item_invoice"            
              loading-text="loading data"
              :loading="loading_invoice"
              :items-per-page="pagination.itemsPerPage"
              :page="pagination.page"
              @update:sort-by="handle_sortBy"
              @update:sort-desc="handle_sortDesc"
              :server-items-length="pagination.itemsLength"
              @pagination="handlePagination"
              :footer-props="{
                'items-per-page-options':[5],
                'disable-items-per-page': true,
              }"
            >
            <!-- :mobile-breakpoint="0"  to disbale vertical row -->
            <template v-slot:top="{ item }">
              
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
              <v-dialog v-model="dialog_push_ulang_invoice" max-width="500px">
                <v-card>
                  <v-card-title class="text-h6" style="word-break: normal; overflow-wrap: break-word; white-space: normal;">data sudah ada di jurnal, apakah ingin push ulang? <p style="color : red">(data yg sudah masuk di jurnal akan di reset)</p></v-card-title>
                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn small color="error"  @click="dialog_push_ulang_invoice = false">Batal</v-btn>
                    <v-btn small color="success"  @click="patchJurnalInvoice()">Lanjut</v-btn>
                    <v-spacer></v-spacer>
                  </v-card-actions>
                </v-card>
              </v-dialog>
            </template>
            <template v-slot:item.actions="{ item }">
             <v-menu
                top
                :offset-x="true"
                rounded="lg"
              >
                <template v-slot:activator="{ on, attrs }">
                  <v-chip
                    color="primary"
                    dark
                    v-bind="attrs"
                    v-on="on"
                    x-small
                    class="mr-2"
                  >
                  <v-icon
                    small
                    class="mr-2"
                    color="white"
                    
                  >
                    mdi-file-pdf-box
                  </v-icon>
                  inv
                  </v-chip>
                </template>

                <v-list>
                  <v-list-item                  >
                      <v-list-item-title @click="exportToPDF_api(item.id, 'invoice')">with stamp</v-list-item-title>
                  </v-list-item>
                  <v-divider />
                  <v-list-item>
                      <v-list-item-title @click="exportToPDF_api(item.id, 'invoice-x')">no stamp</v-list-item-title>
                  </v-list-item>
                </v-list>
              </v-menu>
              

              <v-chip
                class="mr-2"
                x-small
                color="info"
                @click="openDialogExportDate(item.id)"
              >
                <v-icon
                small
                class="mr-2"
                color="white"
                
              >
                mdi-file-pdf-box
              </v-icon>
                 sj
              </v-chip>
              <v-chip
                class="mr-2"
                x-small
                color="blue"
                @click="pushJurnalInvoice(item.id)"
                style="color: white;"
              >
                <v-icon
                small
                class="mr-2"
                color="white"
                >
                  mdi mdi-file-arrow-left-right-outline
                </v-icon>
              Jurnal.id
              </v-chip>
              <!-- <v-chip 
                class="mr-2"
                x-small
                color="cyan"
                dark
                @click="isAddingMemo = true; memoToDownload = item.id"
              >
                <v-icon
                small
                class="mr-2"
                color="white"
                
              >
                mdi-file-pdf-box
              </v-icon>
                 mm -->
              <!-- </v-chip> -->
              <!-- <v-icon
                small
                class="mr-2"
                color="success"
                @click="show_invoice(item.id)"
              >
                mdi-pencil
              </v-icon> -->
              <v-icon
                class="mr-2"
                small
                color="secondary"
                @click="isAddingFile = true; selected_inv = item.id"
              >
                mdi-paperclip
              </v-icon>

              <v-icon
                small
                color="error"
                @click="show_dialog_delete(item.id)"

              >
                mdi-delete
              </v-icon>
              <!-- <v-icon
                small
                class="mr-2"
                :color="(item.id == selected_inv) ? 'success' : 'secondary'"
                @click="preview_func(item.id)"
              >
                mdi-eye
              </v-icon> -->
            </template>
            <template v-slot:item.total_invoice ="{ item }">
              {{ item.total_invoice | rupiah }}
            </template>
            <template v-slot:item.tanggal_invoice="{ item }">
              {{ item.tanggal_invoice | tanggal_id }}
            </template>
            <template v-slot:item.inv_document="{item}">
               <!-- {{ item.sc_dokumen_id ? item.document.name : '-' }} -->
              <div v-if="item.inv_dokumen_id ">
                <a  @click="download_file(item.inv_document.file.id)">
                    Unduh
                    <v-icon
                    class="mr-2"
                    small
                    color="secondary"
                    >
                    mdi-download
                  </v-icon>
                </a>
                |
                <a>
                  <v-icon
                    small
                    color="error"
                    @click="delete_file(item.id, 'invoice')"
                  >
                    mdi-delete
                  </v-icon>
                </a>
              </div>              
              
              <div v-else>
                -  
              </div>
            </template>
            <template v-slot:item.fak_document="{item}">
               <!-- {{ item.sc_dokumen_id ? item.document.name : '-' }} -->
              <div v-if="item.fak_dokumen_id ">
                <a  @click="download_file(item.fak_document.file.id)">
                    Unduh
                    <v-icon
                    class="mr-2"
                    small
                    color="secondary"
                    >
                    mdi-download
                  </v-icon>
                </a>
                |
                <a>
                  <v-icon
                    small
                    color="error"
                    @click="delete_file(item.id, 'faktur')"
                  >
                    mdi-delete
                  </v-icon>
                </a>
              </div>              
              
              <div v-else>
                -  
              </div>
            </template>
            <template v-slot:item.status_jurnal="{item}">
              <!-- {{ item.status_jurnal }} -->
              <div v-if ="loading_status_invoice == false">
                <v-chip
                v-if="item.status_jurnal == 'overdue'"
                class="ma-2"
                
                color="orange"
                label
                outlined
                >
                  overdue
                </v-chip>
                <v-chip
                  v-if="item.status_jurnal == 'lunas'"
                  class="ma-2"
                  color="success"
                  label
                  outlined
                >
                  Paid
                </v-chip>
                <v-chip
                  v-if="item.status_jurnal == 'belum push'"
                  class="ma-2"
                  color="error"
                  label
                  outlined
                >
                  x
                </v-chip>
              </div>
             
              <v-progress-circular
                v-if = "loading_status_invoice == true"
                indeterminate
                color="blue"
                :size="20"
              ></v-progress-circular>
               <!-- {{ item.status_jurnal }} -->
            </template>
          
            </v-data-table>
          </v-card>
          <div :class="(preview_pdf == true) ? '' : 'd-none'" id="cetak2">
            <CetakPdf 
              :form_sc_prop = "form_sc" 
              :mode ="mode"
            />
          </div>
          <v-overlay
            :absolute="false"
            :value="isAddingInvoice"
          >
            <v-card 
              
              light
            >
              <v-card-title v-if="isEditingInvoice == false">Sales Contract: </v-card-title>
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
                  @click="pilih_item_invoice()"
                  :disabled="loading_simpan"
                  v-if="isEditingInvoice == false"            
                >
                  <div v-if="loading_simpan == false">Tampilkan</div>
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
          <v-overlay
            :absolute="false"
            :value="isAddingMemo"
          >
            <v-card 
              max-height="400px"
              light
            >
              <v-card-title>Info tambahan: </v-card-title>
              <v-divider></v-divider>
              
              <v-card max-height="200px" class="overflow-auto" elevation="0"> 
                <div class="mx-4 mt-4">
                tanggal memo :
                </div>
                <v-col cols="12" class="py-0">
                    <v-dialog
                      ref="dialog2"
                      v-model="modal2"
                      :return-value.sync="date_mm"
                      persistent
                      width="290px"
                    >
                      <template v-slot:activator="{ on, attrs2 }">
                        <v-text-field            
                          v-model="date_mm"
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
                        v-model="date_mm"
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
                          @click="$refs.dialog2.save(date_mm)"
                        >
                          OK
                        </v-btn>
                      </v-date-picker>
                  </v-dialog>
                </v-col> 
                <div class="mx-4 mt-2">
                Suplier :
                </div> 
                <v-col
                  class="d-flex pb-0"
                  cols="12"
                >
                  <v-text-field
                      v-model="mm_supplier"
                      placeholder="nama suplier"
                      dense
                      outlined
                      class="mt-0 py-0"
                      clearable
                  >
                  </v-text-field>
                </v-col>
                <div class="mx-4 mt-2">
                Alamat suplier :
                </div> 
                <v-col
                  class="d-flex pb-0"
                  cols="12"
                >
                  <v-text-field
                      v-model="mm_alamat_supplier"
                      placeholder="alamat"
                      dense
                      outlined
                      class="mt-0 py-0"
                      clearable
                  >
                  </v-text-field>
                </v-col>
                <div class="mx-4 mt-2">
                Keterangan :
                </div> 
                <v-col
                  class="d-flex pb-0"
                  cols="12"
                >
                  <v-text-field
                      v-model="keterangan"
                      placeholder="keteranngan"
                      dense
                      outlined
                      class="mt-0 py-0"
                      clearable
                  >
                  </v-text-field>
                </v-col>
                <div class="mx-4 mt-0">
                Sopir :
                </div> 
                <v-col
                  class="d-flex pb-0"
                  cols="12"
                >
                  <v-text-field
                      v-model="sopir"
                      placeholder="sopir"
                      dense
                      outlined
                      class="mt-0 py-0"
                      clearable
                  >
                  </v-text-field>
                </v-col>
                <div class="mx-4 mt-0">
                Nopol :
                </div> 
                <v-col
                  class="d-flex pb-0"
                  cols="12"
                >
                  <v-text-field
                      v-model="nopol"
                      placeholder="nopol"
                      dense
                      outlined
                      class="mt-0 py-0"
                      clearable
                  >
                  </v-text-field>
                </v-col>
              </v-card>
              <v-divider></v-divider>
              <v-card-actions class="d-flex justify-end">            
                <v-btn
                  small
                  class=""
                  :width="60"
                  color="error"
                  @click="isAddingMemo = false; clear_form_memo()"
                  :disabled="loading_simpan"
                >
                  <div>batal</div>                
                </v-btn>
                <v-btn
                  small
                  class=""
                  :width="60"
                  color="success"
                  @click="exportToPDF_api(memoToDownload, 'memo'); isAddingMemo = false;"
                  :disabled="loading_simpan"
                  v-if="isEditingInvoice == false"            
                >
                  <div v-if="loading_simpan == false"><v-icon>mdi-download</v-icon></div>
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
          <v-overlay
            :absolute="false"
            :value="isAddingFile"
          >
          <v-card 
            light
            class="pa-2"
          >
            <v-card-title>upload dokumen: </v-card-title>
            <v-divider></v-divider>
              <v-sheet
                class="mx-auto mt-5 mb-5"
                max-width="700"
              >
                <v-slide-group
                   v-model="selected_upload_doc"
                   mandatory
                >
                  <v-slide-item
                     v-slot="{ active, toggle }"
                     value = "invoice"
                  >
                    <v-btn
                      class="mr-2"
                      :input-value="active"
                      active-class="purple white--text"
                      depressed
                      rounded
                      @click="toggle"
                      x-small
                    >
                      Invoice
                    </v-btn>
                  </v-slide-item>
                  <v-slide-item
                     v-slot="{ active, toggle }"
                     value = "faktur"
                  >
                    <v-btn
                      class=""
                      :input-value="active"
                      active-class="purple white--text"
                      depressed
                      rounded
                      @click="toggle"
                      x-small
                    >
                      Faktur
                    </v-btn>
                  </v-slide-item>
                </v-slide-group>
              </v-sheet>     
              <!-- <input type="file" @change="handleFileUpload($event)" /> -->
              <v-file-input
                type="file"
                show-size
                truncate-length="15"
                @change="handleFileUpload"
              ></v-file-input>
            <v-divider></v-divider>
            <v-card-actions class="d-flex justify-end">
                    
              <v-btn
                small
                class=""
                color="success"
                @click="isAddingFile = false; uploadFile();"
              >
                simpan
              </v-btn>
              <v-btn
                small
                class=""
                color="error"
                @click="isAddingFile = false;"
              >
                batal
              </v-btn>
            </v-card-actions>          
          </v-card>
        </v-overlay>  
        </v-container>    
      </v-card>
      <v-card class="logo" color="primary" elevation="5" v-if="show_items_sc_detail">
          <v-img 
              src="/card_background.jpg" 
              max-height="100"
              max-width="100%"
              style="position: absolute; top: 0px; right: 0px; opacity: 0.2; border-radius:10px">
          </v-img>
          <v-card-title class="">
              <span style="color:white; position: absolute; z-index: 1;" class="mr-5"> 
              TAMBAH ke INVOICE - {{selected_no_sc}}
              </span>
              <v-spacer></v-spacer>
              <v-btn small rounded color="error" class="mr-3" @click="clear_item_form()">
                <v-icon small>mdi-close</v-icon>
                <span v-if="$vuetify.breakpoint.name == 'md'">clear</span>
              </v-btn>
          </v-card-title>
      </v-card>

      <div id="tambah_item_invoice"></div>
      <v-card class="logo py-4 mb-10" elevation="5" v-if="show_items_sc_detail">      
        <v-container>
          <v-card outlined>
            <v-data-table
              :headers="headers_inv_item"
              :items="items_sc_detail"
              class="elevation-1"
              hide-default-footer
              :loading = "loading_simpan"
              :items-per-page = "100"
            >
              <template v-slot:item.checklist="{ item }">
                <!-- <v-simple-checkbox
                   v-model="item.checklist"
                ></v-simple-checkbox> -->
                <input type="checkbox" v-model="checklist_sc" :value ="item.id"></input>
              </template>
            </v-data-table>
          </v-card>
          <div class="d-flex justify-end">
            <v-btn @click="simpan_invoice()" :disabled="loading_simpan || checklist_sc.length == 0" class="mt-3" color="success">
              simpan
            </v-btn>
          </div>
        </v-container>
      </v-card>
      <!-- <div v-for="item in selected_sc_item">{{ item.id }}</div> -->
       <v-dialog
          v-model="dialogExportDate"
          persistent
          max-width="400px"
        >
          <v-card>
            <v-card-title class="text-h5">Pilih Tanggal</v-card-title>
            <v-card-text>
              <v-dialog
                ref="dialog2"
                v-model="modal2"
                :return-value.sync="exportDate"
                persistent
                width="290px"
              >
                <template v-slot:activator="{ on, attrs2 }">
                  <v-text-field            
                    v-model="exportDate"
                    label="tanggal sj"
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
                  v-model="exportDate"
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
                    @click="$refs.dialog2.save(exportDate)"
                  >
                    OK
                  </v-btn>
                </v-date-picker>
              </v-dialog>
            </v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="dialogExportDate = false">
                Batal
              </v-btn>
              <v-btn color="blue darken-1" text @click="exportToPDF_api(selected_inv, 'sj')">
                OK
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
    </v-container>
</template>

<script>
import moment from 'moment';
import { Capacitor } from '@capacitor/core';
import { Filesystem, Directory } from '@capacitor/filesystem';
import { FileOpener } from '@capacitor-community/file-opener';

    export default {
        mounted(){
          this.html2pdf = require('html2pdf.js');
          this.search_invoice_func();
          // this.get_sales_contract();
        },
        created(){
          this.date_invoice = this.$moment().format('YYYY-MM-DD');
          this.date_mm = this.$moment().format('YYYY-MM-DD');
          this.exportDate = this.$moment().format('YYYY-MM-DD');
        },
        data(){
          return {
              show_items_sc_detail : false,
              checklist_sc : [],
              selected_sc_item : '', 
              mm_supplier : '',
              mm_alamat_supplier: '',
              selected_inv : '',
              preview_pdf : false,
              mode : 'invoice',
              form_sc : {
                sc_id             : '',
                date              : '',
                customer          : '',
                customer_json     : '',
                products          : '',
                products_json     : '',
                grand_total_rp    : '',
                grand_total_qty   : '',
                grand_total       : '',
                ongkir            : '',
                sales_contract_no : '',
                customer_id       : '',
              },
              date_invoice_search : '',
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
              isAddingMemo: false,
              loading_invoice : false,
              pagination: {
                    page: 1, // Current page
                    itemsPerPage: 5,
                    itemsLength:0, // Number of items per page
                  },
              headers_inv_item: [
                        { text: 'no'              , value: 'no' },
                        { text: 'Jenis Barang'    , value: 'jenis_barang' },
                        { text: 'Code Coil'       , value: 'code_coil' },
                        { text: 'Qty'             , value: 'qty' },
                        { text: 'Length'          , value: 'total_mtr' },
                        { text: 'Harga'           , value: 'harga' },
                        { text: 'Keterangan'      , value: 'keterangan' },
                        { text: 'checklist'       , value: 'checklist' },
                  ],
              items_sc_detail :[],
              selected_no_sc : '',
              selected_no_inv : '',
              headers_sc : [
                  {
                      text: 'No',
                      align: 'start',
                      sortable: true,
                      value: 'no',
                  },
                  {
                      text: 'No Invoice',
                      align: 'start',
                      sortable: false,
                      value: 'nomor_invoice',
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
                      sortable: true,
                      value: 'tanggal_invoice',
                  },
                  {
                      text: 'total contract',
                      align: 'start',
                      sortable: true,
                      value: 'total_invoice',
                  },
                  { text: 'Actions', value: 'actions', sortable: false },
                  { text: 'Dokumen invoice', value: 'inv_document', sortable: false },
                  { text: 'Dokumen faktur', value: 'fak_document', sortable: false },
                  { text: 'status Jurnal', value: 'status_jurnal', sortable: false },
              ],
              search_sc : '',                     
              date_invoice : '',
              date_mm : '',
              date_sc : '',
              modal2 : false,
              item_invoice : [],
              items_sc : [],
              sortBy : '',
              sortDesc: false,
              keterangan:'',
              sopir:'',
              nopol:'',
              memoToDownload: '',
              isAddingFile : false,
              selected_upload_doc: 'invoice',
              jurnal_token : process.env.JURNAL_TKN,
              pushed_to_jurnal : false,
              is_fail_to_jurnal : false,
              dialog_push_ulang_invoice : false,
              jurnal_invoice_form : '',
              loading_status_invoice : false,
              dialogExportDate: false,
              exportDate: null,
              openDatePicker: false,
          }
        },
        computed : {
          height () {
            switch (this.$vuetify.breakpoint.name) {
              case 'xs': return 200
              case 'sm': return 200
              case 'md': return 75
              case 'lg': return 75
              case 'xl': return 75
            }
          },
          hide_bar () {
            switch (this.$vuetify.breakpoint.name) {
              case 'xs': return true
              case 'sm': return true
              case 'md': return false
              case 'lg': return false
              case 'xl': return false
            }
          },
         
          form_invoice() {
            const form = {
              sales_contract_id  : this.selected_sc,
              tanggal_invoice : this.date_invoice,
              items : this.checklist_sc
              // items : 
            }

            return form
          },
          form_mm() {
            const form = {
              keterangan  : this.keterangan,
              sopir : this.sopir,
              nopol : this.nopol,
              nama_supplier : this.mm_supplier,
              alamat_supplier : this.mm_alamat_supplier,
              tanggal_berangkat : this.date_mm
            }

            return form
          },
        },  
        methods :  {
          async pushJurnalInvoice(id){
            let inv =  this.item_invoice.find(data => data.id == id); 
            this.selected_no_inv = inv.nomor_invoice;
            let encodedParam = encodeURIComponent(inv.nomor_invoice);
    
            //cek apakah sudah pernah push to jurnal
            let cekJurnalinvoice = await this.getJurnalInvoice(encodedParam);
            console.log(cekJurnalinvoice);

            //form insert jurnal
            let form = {
              "sales_invoice": {
                "transaction_date": inv.tanggal_invoice,
                "transaction_lines_attributes": [
                  {
                    "quantity": 1,
                    "rate": inv.total_invoice,
                    "discount": 0,
                    "product_name": "COIIL",
                    "line_tax_name": "PPN"
                  }
                ],
                "shipping_date": inv.tanggal_invoice,
                "shipping_price": 0,
                "shipping_address": "Test Street",
                "is_shipped": false,
                "ship_via": "-",
                "reference_no": "-",
                "tracking_no": "-",
                "address": inv.sales_contract.customer.alamat,
                "term_name": "Custom",
                "due_date": inv.tanggal_invoice,
                "deposit_to_id": 90543234,
                "deposit": 0,
                "discount_unit": 0,
                "witholding_account_name": "Piutang Usaha",
                "witholding_value": 0,
                "witholding_type": "percent",
                "discount_type_name": "percent",
                "person_name": inv.sales_contract.customer.name.trim(),
                "transaction_no": inv.nomor_invoice,
                "message": "-",
                "memo": "-",
                "custom_id": inv.id,
                "source": "Arion Push",
                "use_tax_inclusive": true,
                "tax_after_discount": false,
                "tax_no" : inv.sales_contract.customer.npwp
              }
            }
            this.jurnal_invoice_form = form;
            
            //do update or insert 
            if (cekJurnalinvoice.status){
              console.log('data ada, melakukan update')
              this.dialog_push_ulang_invoice = true;
              // this.patchJurnalInvoice(encodedParam,form)

            } else {
              console.log('data belum ada, melakukan insert')
              this.postJurnalInvoice(form)
         
            }

          },

          patchJurnalInvoice(){
            this.dialog_push_ulang_invoice = false;
            this.loading_invoice = true;
            let encodedParam = encodeURIComponent(this.selected_no_inv);
            this.$axios.patch('https://api.jurnal.id/partner/core/api/v1/sales_invoices/'+encodedParam,this.jurnal_invoice_form, 
                  {
                    headers: {
                      'Accept': 'application/json', 
                      'Authorization': 'Bearer '+this.jurnal_token 
                    }})
              .then(response => {
                console.log(response);
                this.loading_invoice = false;
                this.pushed_to_jurnal = true;
                this.search_invoice_func(this.pagination.page);
              })
              .catch(error => {
                console.log(error);
                this.loading_invoice = false;
                this.is_fail_to_jurnal = true;
                this.search_invoice_func(this.pagination.page);
              })
          },
          createCustomerPromise(customerDetails) {
            return new Promise((resolve, reject) => {
              this.$axios.post(
                'https://api.jurnal.id/partner/core/api/v1/customers',
                customerDetails,
                {
                  headers: {
                    Accept: 'application/json',
                    Authorization: 'Bearer ' + this.jurnal_token,
                  },
                }
              )
              .then(response => {
                console.log('Customer created successfully');
                resolve(response);
              })
              .catch(error => {
                reject(error);
              });
            });
          },
          postJurnalInvoice(form) {
            this.loading_invoice = true;
            this.$axios
              .post('https://api.jurnal.id/partner/core/api/v1/sales_invoices', form, {
                headers: {
                  Accept: 'application/json',
                  Authorization: 'Bearer ' + this.jurnal_token,
                },
              })
              .then((response) => {
                console.log(response);
                this.loading_invoice = false;
                this.pushed_to_jurnal = true;
                this.search_invoice_func(this.pagination.page);
              })
              .catch(async (error) => {
                console.log(error);

                // Check if the error is related to a missing person_name
                if (
                  error.response &&
                  error.response.data &&
                  error.response.data.person_name === 'person not exist'
                ) {
                  console.log('Customer does not exist. Creating customer...');

                  // Extract customer details from the invoice
                  const inv = form.sales_invoice;
                  const customerDetails = {
                    customer: {
                      title: '',
                      first_name: '',
                      middle_name: '',
                      last_name: '',
                      display_name: inv.person_name,
                      associate_company: inv.person_name,
                      billing_address: '',
                      address: inv.address,
                      phone: '',
                      fax: '',
                      mobile: '',
                      email: '',
                      disable_max_credit_limit: '',
                      max_credit_limit: '',
                      start_balance: '',
                      opening_balance: 0,
                      default_ar_account_name: 'Piutang Usaha',
                      default_ap_account_name: 'Hutang Usaha',
                      source: 'api',
                      tax_no: inv.tax_no,
                      other_detail: '',
                      custom_id: '',
                    },
                  };

                 
                  // Panggil createCustomerPromise, lalu post invoice setelah selesai
                  this.createCustomerPromise(customerDetails)
                    .then(() => {
                      return this.postJurnalInvoice(form);
                    })
                    .catch(customerError => {
                      console.error('Failed to create customer:', customerError);
                      this.loading_invoice = false;
                      this.is_fail_to_jurnal = true;
                      this.search_invoice_func(this.pagination.page);
                    });

                    return;
                } else {
                  // Other errors
                  this.loading_invoice = false;
                  this.is_fail_to_jurnal = true;
                  this.search_invoice_func(this.pagination.page);
                }
              });
          },

          getJurnalInvoice(no_invoice) {
            return new Promise( resolve => {
               // Encodes to "INV%2FAPS%2F001%2F03%2F2024"
              this.$axios.get('https://api.jurnal.id/partner/core/api/v1/sales_invoices/'+no_invoice, 
                  {
                    headers: {
                      'Accept': 'application/json', 
                      'Authorization': 'Bearer '+this.jurnal_token 
                    }})
              .then(response => {
                // console.log(response);
                resolve({status : true, data_jurnal : response.data});
              })
              .catch(error => {
                // console.log(error);
                resolve({status : false, error : error});
              })
            })
          },
          handleFileUpload(file) {
            console.log(file)
            this.file = file;
          },
          async uploadFile() {
            if (!this.file) {
              alert('Please select a file to upload.');
              return;
            } else {

              const formData = new FormData();
              formData.append('file', this.file);            
              formData.append('inv_id', this.selected_inv);
              formData.append('tipe', this.selected_upload_doc);

              this.loading_invoice = true;
              try {
                const response = await this.$axios.post('/invoice/upload-doc', formData, {
                  headers: {
                    'Content-Type': 'multipart/form-data',
                  },
                });
                console.log(response.data);
                this.file = null;
                this.search_invoice_func(this.pagination.page);
                this.selected_inv = '';
                this.loading_invoice = false;
              } catch (error) {
                console.error(error);
                this.selected_inv = '';
                this.loading_invoice = false;
              }
            }
          },
          async delete_file(sc_id, tipe) {
          try {
            this.loading_invoice = true;
            this.item_invoice = [];
            const response = await this.$axios.get('/delete_file_doc/'+sc_id+'/'+tipe);
            this.search_invoice_func();
            console.log(response);
            this.loading_invoice = false;
          } catch (error) {
            console.error('Error deleting the file:', error);
            this.loading_invoice = false;
          }
        },
        async download_file(file_id) {
          try {
            const response = await this.$axios.get('/download/'+file_id, {
              responseType: 'blob'
            });
            const url = window.URL.createObjectURL(new Blob([response.data]));
            const link = document.createElement('a');
            link.href = url;
            link.setAttribute('download', 'file.pdf'); // Change the file name and extension as needed
            document.body.appendChild(link);
            link.click();
            link.remove();
          } catch (error) {
            console.error('Error downloading the file:', error);
          }
        },
          clear_item_form(){
              this.items_sc = [];
              this.isAddingInvoice = false;
              this.checklist_sc = [];
              this.items_sc_detail = [];
              this.show_items_sc_detail = false;
          },
          pilih_item_invoice(){
            this.show_items_sc_detail = true;
            this.$vuetify.goTo('#tambah_item_invoice');
            console.log('item sc show')
              if(this.items_sc.length >= 1){
                this.items_sc[0].item.forEach((data, index) => {
                  // data.checklist = true;
                  //cek if already invoiced

                  if(data.invoiced == false){
                    data.no = index+1;
                    this.items_sc_detail.push(data);
                  }
                })
                this.selected_no_sc = this.items_sc[0].nomor_sc;
              }
            this.isAddingInvoice = false;
            //  this.items_sc_detail 
          },
          clear_form_memo(){
            this.mm_supplier = '';
            this.mm_alamat_supplier = '';
            this.keterangan = '';
            this.sopir = '';
            this.nopol = '';
          },
          wait(ms) {
            return new Promise(resolve => {
              setTimeout(resolve, ms);
            });
          },
          async preview_func(id){
            if (this.selected_inv == id){
              this.preview_pdf = false
              this.selected_inv = ''
            }else{
              this.preview_pdf = true;
              await this.wait(1000);
              this.$vuetify.goTo('#cetak2')
              this.selected_inv = id;
              this.show_invoice(id);
            }
          },

          async exportToPDF(id, doc){
            
            if (doc == 'sj'){
              this.mode = 'sj'
            } 
            if (doc == 'invoice'){
              this.mode = 'invoice'
            } 

            let fetch_invoice = await this.show_invoice(id);
            if (fetch_invoice){
                console.log('telah fetch')
                const options = {
                  margin: [5, 5, 5, 5], // Set the margins of the PDF
                  filename: this.form_sc.customer.nama+'_'+this.$moment().format('YYYY-MM-DD'), // Set the name of the PDF file
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
          openDialogExportDate(id){
            this.dialogExportDate = true;
            this.selected_inv = id;
          },
          async exportToPDF_api(id,doc) {
            let tipe = '';
            let stamp = true;
            if (doc == 'sj'){
              tipe = 'sj';
            } 
            if (doc == 'invoice'){
              tipe = 'inv';
            } 
            if (doc == 'invoice-x'){
              tipe = 'inv';
              stamp = false;
            } 
            if (doc == 'memo'){
              tipe = 'mm';
            } 

            const inv = this.item_invoice.find(x => x.id === id);
            let kodeInvoice = inv && inv.nomor_invoice ? inv.nomor_invoice.replace(/[\\/:*?"<>|]/g, '') : 'kode';
            let customerName = inv && inv.sales_contract && inv.sales_contract.customer && inv.sales_contract.customer.name
              ? inv.sales_contract.customer.name.replace(/[\\/:*?"<>|]/g, '')
              : 'customer';
            let tanggalInvoice = inv && inv.tanggal_invoice
              ? this.$moment(inv.tanggal_invoice).format('DD-MM-YYYY')
              : this.$moment().format('DD-MM-YYYY');

            if (doc === 'invoice' || doc === 'invoice-x') {
              kodeInvoice = kodeInvoice.replace(/^INV/i, 'INV');
            }
            if (doc === 'sj') {
              kodeInvoice = kodeInvoice.replace(/^INV/i, 'SJ');
            }

            const filename = `${customerName}_${kodeInvoice}_${tanggalInvoice}.pdf`;
            const androidFilename = filename.replace(/\//g, '_');


            let fetch_invoice = await this.show_invoice(id);
            if(fetch_invoice){
              this.$axios.post('/download-pdf',{id : id, tipe : tipe, tanggal_sj : this.exportDate, info_mm : this.form_mm, stamp : stamp},{ responseType: 'blob' })
                  .then(response => {   
                    
                    if (Capacitor.getPlatform() === 'android') {
                    // Android-specific handling
                      console.log('Running on Android');
                      
                      // Convert Blob to base64
                      const reader = new FileReader();
                      reader.readAsDataURL(response.data);
                      reader.onloadend = async () => {
                        const base64Data = reader.result.split(',')[1];

                        try {
                          // Write the file
                          const pdfFile = await Filesystem.writeFile({
                            path: 'secrets/' + androidFilename,
                            data: base64Data,
                            directory: Directory.External,
                            recursive: true,
                          });

                          // Open the file
                          await FileOpener.open({
                            filePath: pdfFile.uri,
                            openWithDefault: true,
                          });
                          console.log('File opened successfully');
                        } catch (e) {
                          console.error(`Unable to open file: ${e.message}`);
                        }
                      };
                      this.clear_form_memo()

                    } else if (Capacitor.getPlatform() === 'web') {
                      // Web-specific handling
                      console.log('Running on a web');
                      
                      // Create a Blob object from the response data
                      const blob = new Blob([response.data], { type: 'application/pdf' });
                      // Create a temporary URL for the Blob
                      const url = window.URL.createObjectURL(blob);
                      // Create a link element and simulate a click to trigger the download
                      const link = document.createElement('a');
                      link.href = url;
                      link.setAttribute('download', filename);
                      document.body.appendChild(link);
                      link.click();
                      // Cleanup
                      window.URL.revokeObjectURL(url);

                      this.clear_form_memo()

                    }
                     
                  })
                  .catch(error => {
                      console.log(error);
                      this.loading_rekap = false;
                  })
            }
          },
          show_dialog_delete(id){
            this.dialog_delete_invoice = true;
            this.invoice_id_todelete = id;
          },
          handle_sortBy(sortBy){
            this.sortBy = sortBy;
            console.log(sortBy);
            if (sortBy){
              this.search_invoice_func();
            }else {
              this.search_invoice_func();
            }
          },
          handle_sortDesc(sortDesc){
            this.sortDesc = sortDesc;
            console.log(sortDesc);
            if (sortDesc){
              this.search_invoice_func()
            } 
          },
         async processJurnalInvoices() {
            this.loading_status_invoice = true;

            // Create an array of promises for all invoices
            const fetchPromises = this.item_invoice.map(async (x) => {
              let data = '';
              let encodedParam = encodeURIComponent(x.nomor_invoice);
              data = await this.getJurnalInvoice(encodedParam); // Fetch data for each invoice
              if (data.status === true) {
                x.status_jurnal = data.data_jurnal.sales_invoice.has_payments === true ? 'lunas' : 'overdue';
              } else {
                x.status_jurnal = 'belum push';
              }
              await this.wait(500); // Add a delay between each request
            });

            // Wait for all fetches to complete
            await Promise.all(fetchPromises);

            this.loading_status_invoice = false;
          },
          handlePagination(pagination) {
            if(this.pagination.page != pagination.page){
              this.loading_invoice = true;
              this.pagination.page = pagination.page;             
              this.item_invoice = [];
              console.log(pagination)
              this.$axios.post('/invoice/search-invoice?page='+pagination.page, {search_invoice : this.search_invoice, date_sc : this.date_invoice_search, sortBy : this.sortBy, sortDesc : this.sortDesc})
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
                  this.loading_invoice = false;
                }).
                then( () => {
                  this.processJurnalInvoices();
                }).then( async ()=> {
                  // await this.wait(1000);
                  // this.loading_status_invoice = false;
                })
                .catch(error => {
                  console.log(error);
                  this.loading_invoice= false;
                })
            }
          },
          get_invoice(){
            this.item_invoice = [];
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
          sort_invoice(){
            this.item_invoice = []
            this.loading_invoice = true;
            this.$axios.post('/invoice/sort-invoice',{sortBy : this.sortBy, sortDesc : this.sortDesc})
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
            this.pagination.page = 1;

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
          search_invoice_func(page = 1) {       
            this.loading_invoice = true;             
            this.item_invoice = [];
            this.pagination.page = page; // Set the current page
            this.$axios.post(`/invoice/search-invoice?page=${this.pagination.page}`, {
              search_invoice: this.search_invoice,
              date_sc: this.date_invoice_search,
              sortBy: this.sortBy,
              sortDesc: this.sortDesc,
            })
            .then(response => {
              response.data.data.data.forEach((x, index) => {
                if (index == 0) {
                  x.no = response.data.data.from;
                } else {
                  x.no = response.data.data.from + index;
                }
                this.item_invoice.push(x);
              });
              this.pagination.itemsLength = response.data.data.total; // Update total items
              this.loading_invoice = false;
            })
            .then(() => {
              this.processJurnalInvoices();
            })
            .then(async () => {
              // await this.wait(1000);
              // this.loading_status_invoice = false;
            })
            .catch(error => {
              console.log(error);
              this.loading_invoice = false;
            });
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
              this.checklist_sc = [];
              this.items_sc_detail = [];
              this.show_items_sc_detail = false;
            })
            .catch(error => {
              console.log(error.data)
              this.loading_simpan = false;
            })

          },
          delete_invoice() {
            this.loading_simpan = true;
            this.$axios.delete('/invoice/'+this.invoice_id_todelete)
            .then(response => {
              this.dialog_delete_invoice = false;  
              this.item_invoice = [];
              this.get_invoice();
              this.loading_simpan = false;
              console.log('berhasil hapus')
            })
            .catch(error => {
              console.log('gagal')
            })
          },
          show_invoice(id){    
            return new Promise( resolve => {
              let sales_contract = '';
              let total_qty = '';
              let customer = '';
              let data_customer = '';
              this.$axios.get('/invoice/'+id)
              .then(response => {
                sales_contract = response.data.data.sales_contract; 
                customer = response.data.data.sales_contract.customer;
                
                data_customer = {
                    nama                :  customer.name,
                    npwp                :  customer.npwp,
                    alamat              :  customer.alamat,
                    alamat_pengambilan  :  sales_contract.alamat_pengambilan,
                    customer_id         :  customer.id
                };
  
                if(sales_contract.item.length > 0){
                  total_qty = sales_contract.item.reduce((sum, item) => sum + Number(item.qty), 0);
                  sales_contract.item.forEach(x => {
                    x.harga_rp = this.convert_rupiah(x.harga);
                    x.total = x.qty * x.harga;
                    x.total_rp = this.convert_rupiah(x.total);                 
                  });
                }
                
                console.log(sales_contract);
                  this.form_sc.sc_id             = sales_contract.id;
                  this.form_sc.date              = response.data.data.tanggal_invoice;
                  this.form_sc.customer          = data_customer;
                  this.form_sc.customer_json     = data_customer;
                  this.form_sc.products          = sales_contract.item;
                  this.form_sc.products_json     = sales_contract.item;
                  this.form_sc.grand_total_rp    = this.convert_rupiah(Number(sales_contract.total));
                  this.form_sc.grand_total_qty   = total_qty;
                  this.form_sc.grand_total       = sales_contract.total;
                  this.form_sc.ongkir            = sales_contract.ongkir;
                  this.form_sc.sales_contract_no = sales_contract.nomor_sc;    
                  resolve(true);                 
              })
              .catch(error => {
                console.log(error)
                resolve(false);
              })
            });                                        
          },
          convert_rupiah(value){
            return Intl.NumberFormat('id', { style: 'currency', currency: 'IDR' }).format(value)
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