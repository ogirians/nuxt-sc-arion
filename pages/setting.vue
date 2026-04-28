<template>
  <v-container fluid class="pa-6">
    <v-row>
      <v-col cols="12">
        <!-- <h1 class="text-h4 font-weight-bold mb-4 secondary--text">Pengaturan Master</h1> -->
        
        <v-card elevation="2" class="rounded-lg overflow-hidden">
          <v-tabs
            v-model="activeTab"
            background-color="transparent"
            color="primary"
            class="px-4 border-bottom"
          >
            <v-tab class="text-capitalize font-weight-medium">
              <v-icon left>mdi-truck-delivery</v-icon>
              Master Supplier
            </v-tab>
            <v-tab class="text-capitalize font-weight-medium">
              <v-icon left>mdi-account-group</v-icon>
              Master Customer
            </v-tab>
          </v-tabs>

          <v-tabs-items v-model="activeTab" class="pa-4">
            <!-- Master Supplier Tab -->
            <v-tab-item>
              <div class="d-flex align-center mb-6">
                <v-text-field
                  v-model="searchSupplier"
                  append-icon="mdi-magnify"
                  label="Cari Supplier..."
                  single-line
                  hide-details
                  outlined
                  dense
                  class="max-width-300 rounded-lg"
                ></v-text-field>
                <v-spacer></v-spacer>
                <v-btn
                  color="primary"
                  class="rounded-lg px-6"
                  depressed
                  @click="openAddSupplierDialog"
                >
                  <v-icon left>mdi-plus</v-icon>
                  Tambah Supplier
                </v-btn>
              </div>

              <v-data-table
                :headers="supplierHeaders"
                :items="suppliers"
                :loading="loadingSupplier"
                :options.sync="optionsSupplier"
                :server-items-length="totalSuppliers"
                :footer-props="{
                  'items-per-page-options': [5, 10, 20, 50]
                }"
                class="elevation-0 border rounded-lg overflow-hidden"
                :header-props="{
                  class: 'grey lighten-4 secondary--text font-weight-bold'
                }"
              >
                <template #[`item.no`]="{ index }">
                  {{ (optionsSupplier.page - 1) * optionsSupplier.itemsPerPage + index + 1 }}
                </template>
                <template #[`item.actions`]="{ item }">
                  <v-btn icon small color="primary" @click="editSupplier(item)" class="mr-2">
                    <v-icon small>mdi-pencil</v-icon>
                  </v-btn>
                  <v-btn icon small color="error" @click="deleteSupplier(item)">
                    <v-icon small>mdi-delete</v-icon>
                  </v-btn>
                </template>
              </v-data-table>
            </v-tab-item>

            <!-- Master Customer Tab -->
            <v-tab-item>
              <div class="d-flex align-center mb-6">
                <v-text-field
                  v-model="searchCustomer"
                  append-icon="mdi-magnify"
                  label="Cari Customer..."
                  single-line
                  hide-details
                  outlined
                  dense
                  class="max-width-300 rounded-lg"
                  @keyup.enter="fetchCustomers"
                ></v-text-field>
                <v-spacer></v-spacer>
                <v-btn
                  color="primary"
                  class="rounded-lg px-6"
                  depressed
                  @click="openAddCustomerDialog"
                >
                  <v-icon left>mdi-plus</v-icon>
                  Tambah Customer
                </v-btn>
              </div>

              <v-data-table
                :headers="customerHeaders"
                :items="customers"
                :loading="loadingCustomer"
                :options.sync="optionsCustomer"
                :server-items-length="totalCustomers"
                :footer-props="{
                  'items-per-page-options': [5, 10, 20, 50]
                }"
                class="elevation-0 border rounded-lg overflow-hidden"
                :header-props="{
                  class: 'grey lighten-4 secondary--text font-weight-bold'
                }"
              >
                <template #[`item.no`]="{ index }">
                  {{ (optionsCustomer.page - 1) * optionsCustomer.itemsPerPage + index + 1 }}
                </template>
                <template #[`item.actions`]="{ item }">
                  <v-btn icon small color="primary" @click="editCustomer(item)" class="mr-2">
                    <v-icon small>mdi-pencil</v-icon>
                  </v-btn>
                  <v-btn icon small color="error" @click="deleteCustomer(item)">
                    <v-icon small>mdi-delete</v-icon>
                  </v-btn>
                </template>
              </v-data-table>
            </v-tab-item>
          </v-tabs-items>
        </v-card>
      </v-col>
    </v-row>

    <!-- Dialog Add/Edit Supplier -->
    <v-dialog v-model="supplierDialog" max-width="600px" persistent>
      <v-card class="rounded-lg">
        <v-card-title class="headline primary white--text px-6 py-4">
          <span class="text-h6 font-weight-bold">{{ dialogTitleSupplier }}</span>
          <v-spacer></v-spacer>
          <v-btn icon dark @click="closeSupplierDialog">
            <v-icon>mdi-close</v-icon>
          </v-btn>
        </v-card-title>
        
        <v-card-text class="pa-6 mt-2">
          <v-form ref="supplierForm">
            <v-row>
              <v-col cols="12">
                <v-text-field
                  v-model="editedSupplier.nama_supplier"
                  label="Nama Supplier"
                  outlined
                  dense
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12" md="6">
                <v-text-field
                  v-model="editedSupplier.telepon_supplier"
                  label="Telepon"
                  outlined
                  dense
                ></v-text-field>
              </v-col>
              <v-col cols="12" md="6">
                <v-text-field
                  v-model="editedSupplier.email_supplier"
                  label="Email"
                  outlined
                  dense
                ></v-text-field>
              </v-col>
              <v-col cols="12">
                <v-textarea
                  v-model="editedSupplier.alamat_supplier"
                  label="Alamat"
                  outlined
                  dense
                  rows="3"
                ></v-textarea>
              </v-col>
            </v-row>
          </v-form>
        </v-card-text>

        <v-card-actions class="pa-6 pt-0">
          <v-spacer></v-spacer>
          <v-btn
            color="grey darken-1"
            text
            class="rounded-lg px-6"
            @click="closeSupplierDialog"
          >
            Batal
          </v-btn>
          <v-btn
            color="primary"
            class="rounded-lg px-6"
            depressed
            :loading="savingSupplier"
            @click="saveSupplier"
          >
            Simpan
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Dialog Add/Edit Customer -->
    <v-dialog v-model="customerDialog" max-width="600px" persistent>
      <v-card class="rounded-lg">
        <v-card-title class="headline primary white--text px-6 py-4">
          <span class="text-h6 font-weight-bold">{{ dialogTitleCustomer }}</span>
          <v-spacer></v-spacer>
          <v-btn icon dark @click="closeCustomerDialog">
            <v-icon>mdi-close</v-icon>
          </v-btn>
        </v-card-title>
        
        <v-card-text class="pa-6 mt-2">
          <v-form ref="customerForm">
            <v-row>
              <v-col cols="12" md="6">
                <v-text-field
                  v-model="editedCustomer.name"
                  label="Nama Customer"
                  outlined
                  dense
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12" md="6">
                <v-text-field
                  v-model="editedCustomer.npwp"
                  label="NPWP"
                  outlined
                  dense
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12">
                <v-text-field
                  v-model="editedCustomer.no_telp"
                  label="No. Telepon"
                  outlined
                  dense
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12">
                <v-textarea
                  v-model="editedCustomer.alamat"
                  label="Alamat"
                  outlined
                  dense
                  rows="3"
                  required
                ></v-textarea>
              </v-col>
            </v-row>
          </v-form>
        </v-card-text>

        <v-card-actions class="pa-6 pt-0">
          <v-spacer></v-spacer>
          <v-btn
            color="grey darken-1"
            text
            class="rounded-lg px-6"
            @click="closeCustomerDialog"
          >
            Batal
          </v-btn>
          <v-btn
            color="primary"
            class="rounded-lg px-6"
            depressed
            :loading="savingCustomer"
            @click="saveCustomer"
          >
            Simpan
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Snackbar for notifications -->
    <v-snackbar
      v-model="snackbar.show"
      :color="snackbar.color"
      :timeout="3000"
      top
      right
      class="mt-12"
    >
      {{ snackbar.text }}
      <template #action="{ attrs }">
        <v-btn text v-bind="attrs" @click="snackbar.show = false"> Tutup </v-btn>
      </template>
    </v-snackbar>
  </v-container>
</template>

<script>
export default {
  name: 'SettingPage',
  data() {
    return {
      activeTab: 0,
      loadingSupplier: false,
      savingSupplier: false,
      loadingCustomer: false,
      savingCustomer: false,
      
      searchSupplier: '',
      supplierDialog: false,
      editedIndexSupplier: -1,
      optionsSupplier: {
        page: 1,
        itemsPerPage: 5,
      },
      totalSuppliers: 0,
      
      searchCustomer: '',
      customerDialog: false,
      editedIndexCustomer: -1,
      optionsCustomer: {
        page: 1,
        itemsPerPage: 5,
      },
      totalCustomers: 0,

      snackbar: {
        show: false,
        text: '',
        color: 'success',
      },
      searchTimeoutSupplier: null,
      searchTimeoutCustomer: null,

      supplierHeaders: [
        { text: 'No', value: 'no', align: 'start', sortable: false, width: '60px' },
        { text: 'Nama Supplier', value: 'nama_supplier', align: 'start' },
        { text: 'Telepon', value: 'telepon_supplier', align: 'start' },
        { text: 'Email', value: 'email_supplier', align: 'start' },
        { text: 'Alamat', value: 'alamat_supplier', align: 'start' },
        { text: 'Aksi', value: 'actions', sortable: false, align: 'center', width: '120px' },
      ],
      suppliers: [],
      editedSupplier: {
        id: null,
        nama_supplier: '',
        alamat_supplier: '',
        telepon_supplier: '',
        email_supplier: '',
      },
      defaultSupplier: {
        id: null,
        nama_supplier: '',
        alamat_supplier: '',
        telepon_supplier: '',
        email_supplier: '',
      },

      customerHeaders: [
        { text: 'No', value: 'no', align: 'start', sortable: false, width: '60px' },
        { text: 'Nama Customer', value: 'name', align: 'start' },
        { text: 'NPWP', value: 'npwp', align: 'start' },
        { text: 'Telepon', value: 'no_telp', align: 'start' },
        { text: 'Alamat', value: 'alamat', align: 'start' },
        { text: 'Aksi', value: 'actions', sortable: false, align: 'center', width: '120px' },
      ],
      customers: [],
      editedCustomer: {
        id: null,
        name: '',
        npwp: '',
        alamat: '',
        no_telp: '',
      },
      defaultCustomer: {
        id: null,
        name: '',
        npwp: '',
        alamat: '',
        no_telp: '',
      },
    }
  },
  computed: {
    dialogTitleSupplier() {
      return this.editedIndexSupplier === -1 ? 'Tambah Supplier Baru' : 'Edit Supplier'
    },
    dialogTitleCustomer() {
      return this.editedIndexCustomer === -1 ? 'Tambah Customer Baru' : 'Edit Customer'
    },
  },
  watch: {
    optionsSupplier: {
      handler() {
        this.fetchSuppliers()
      },
      deep: true,
    },
    searchSupplier(val) {
      if (this.searchTimeoutSupplier) clearTimeout(this.searchTimeoutSupplier)
      this.searchTimeoutSupplier = setTimeout(() => {
        if (this.optionsSupplier.page !== 1) {
          this.optionsSupplier.page = 1
        } else {
          this.fetchSuppliers()
        }
      }, 500)
    },
    optionsCustomer: {
      handler() {
        this.fetchCustomers()
      },
      deep: true,
    },
    searchCustomer(val) {
      if (this.searchTimeoutCustomer) clearTimeout(this.searchTimeoutCustomer)
      this.searchTimeoutCustomer = setTimeout(() => {
        // Reset to first page when searching
        if (this.optionsCustomer.page !== 1) {
          this.optionsCustomer.page = 1
        } else {
          this.fetchCustomers()
        }
      }, 500)
    },
  },
  mounted() {
    // No need to call fetch here as watchers will handle initial load
  },
  methods: {
    // --- Supplier Methods ---
    async fetchSuppliers() {
      this.loadingSupplier = true
      const { page, itemsPerPage } = this.optionsSupplier
      try {
        const response = await this.$axios.get('/suppliers', {
          params: {
            page: page,
            per_page: itemsPerPage,
            nama_supplier: this.searchSupplier
          }
        })
        
        if (response.data.data && response.data.data.data) {
          this.suppliers = response.data.data.data
          this.totalSuppliers = response.data.data.total
        } else if (Array.isArray(response.data.data)) {
          this.suppliers = response.data.data
          this.totalSuppliers = response.data.data.length
        } else {
          this.suppliers = []
          this.totalSuppliers = 0
        }
      } catch (error) {
        this.showSnackbar('Gagal mengambil data supplier', 'error')
      } finally {
        this.loadingSupplier = false
      }
    },
    openAddSupplierDialog() {
      this.editedIndexSupplier = -1
      this.editedSupplier = Object.assign({}, this.defaultSupplier)
      this.supplierDialog = true
    },
    editSupplier(item) {
      this.editedIndexSupplier = this.suppliers.indexOf(item)
      this.editedSupplier = Object.assign({}, item)
      this.supplierDialog = true
    },
    async deleteSupplier(item) {
      if (confirm('Apakah Anda yakin ingin menghapus supplier ini?')) {
        try {
          await this.$axios.delete(`/suppliers/${item.id}`)
          this.showSnackbar('Supplier berhasil dihapus')
          this.fetchSuppliers()
        } catch (error) {
          this.showSnackbar('Gagal menghapus supplier', 'error')
        }
      }
    },
    closeSupplierDialog() {
      this.supplierDialog = false
      this.$nextTick(() => {
        this.editedSupplier = Object.assign({}, this.defaultSupplier)
        this.editedIndexSupplier = -1
      })
    },
    async saveSupplier() {
      this.savingSupplier = true
      try {
        if (this.editedIndexSupplier > -1) {
          await this.$axios.put(`/suppliers/${this.editedSupplier.id}`, this.editedSupplier)
          this.showSnackbar('Supplier berhasil diperbarui')
        } else {
          await this.$axios.post('/suppliers', this.editedSupplier)
          this.showSnackbar('Supplier berhasil ditambahkan')
        }
        this.fetchSuppliers()
        this.closeSupplierDialog()
      } catch (error) {
        const message = error.response?.data?.message || 'Gagal menyimpan data'
        this.showSnackbar(message, 'error')
      } finally {
        this.savingSupplier = false
      }
    },

    // --- Customer Methods ---
    async fetchCustomers() {
      this.loadingCustomer = true
      const { page, itemsPerPage } = this.optionsCustomer
      try {
        const response = await this.$axios.get('/customers', {
          params: {
            page: page,
            per_page: itemsPerPage,
            name: this.searchCustomer
          }
        })
        
        if (response.data.data && response.data.data.data) {
          this.customers = response.data.data.data
          this.totalCustomers = response.data.data.total
        } else if (Array.isArray(response.data.data)) {
          this.customers = response.data.data
          this.totalCustomers = response.data.data.length
        } else {
          this.customers = []
          this.totalCustomers = 0
        }
      } catch (error) {
        this.showSnackbar('Gagal mengambil data customer', 'error')
      } finally {
        this.loadingCustomer = false
      }
    },
    openAddCustomerDialog() {
      this.editedIndexCustomer = -1
      this.editedCustomer = Object.assign({}, this.defaultCustomer)
      this.customerDialog = true
    },
    editCustomer(item) {
      this.editedIndexCustomer = this.customers.indexOf(item)
      this.editedCustomer = Object.assign({}, item)
      this.customerDialog = true
    },
    async deleteCustomer(item) {
      if (confirm('Apakah Anda yakin ingin menghapus customer ini?')) {
        try {
          await this.$axios.delete(`/customers/${item.id}`)
          this.showSnackbar('Customer berhasil dihapus')
          this.fetchCustomers()
        } catch (error) {
          this.showSnackbar('Gagal menghapus customer', 'error')
        }
      }
    },
    closeCustomerDialog() {
      this.customerDialog = false
      this.$nextTick(() => {
        this.editedCustomer = Object.assign({}, this.defaultCustomer)
        this.editedIndexCustomer = -1
      })
    },
    async saveCustomer() {
      this.savingCustomer = true
      try {
        if (this.editedIndexCustomer > -1) {
          await this.$axios.put(`/customers/${this.editedCustomer.id}`, this.editedCustomer)
          this.showSnackbar('Customer berhasil diperbarui')
        } else {
          await this.$axios.post('/customers', this.editedCustomer)
          this.showSnackbar('Customer berhasil ditambahkan')
        }
        this.fetchCustomers()
        this.closeCustomerDialog()
      } catch (error) {
        const message = error.response?.data?.message || 'Gagal menyimpan data'
        this.showSnackbar(message, 'error')
      } finally {
        this.savingCustomer = false
      }
    },

    showSnackbar(text, color = 'success') {
      this.snackbar.text = text
      this.snackbar.color = color
      this.snackbar.show = true
    },
  },
}
</script>

<style scoped>
.max-width-300 {
  max-width: 300px;
}
.border-bottom {
  border-bottom: 1px solid rgba(0, 0, 0, 0.12);
}
.v-data-table >>> thead th {
  font-size: 0.875rem !important;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}
</style>




