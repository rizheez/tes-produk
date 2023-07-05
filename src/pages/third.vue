<script setup>
import axios from "@axios";
import { reactive, ref } from "vue";

import { VDataTable } from "vuetify/labs/VDataTable";

const produk = reactive({
  produk: "",
  kategori: "",
  supplier: "",
  stok: "",
  harga: "",
});

const search = ref("");
const isDialogVisible = ref(false);
const product = ref([]);

const headers = [
  {
    title: "ID",
    sortable: false,
    key: "id",
  },
  {
    title: "produk",
    key: "produk",
  },
  {
    title: "kategori",
    key: "kategori",
  },
  {
    title: "supplier",
    key: "supplier",
  },
  {
    title: "stok",
    key: "stok",
  },
  {
    title: "harga",
    key: "harga",
  },
  {
    title: "ACTIONS",
    key: "actions",
  },
];

const fetchProducts = async () => {
  try {
    const response = await axios.get("http://127.0.0.1:8000/api/produk");
    product.value = response.data;
    console.log(product);
  } catch (error) {
    console.error(error);
  }
};

const openDialog = () => {
  isDialogVisible.value = true;
};

const closeDialog = () => {
  isDialogVisible.value = false;
};

const submitForm = async () => {
  try {
    const response = await axios.post(
      "http://127.0.0.1:8000/api/produk",
      produk
    );
    if (response.status === 201) {
      console.log("Data Berhasil Disimpan ====>", response.data);
    }
    resetForm();
    closeDialog();
    await fetchProducts(); // Mengambil data produk terbaru setelah menyimpan
  } catch (error) {
    console.error(error);
  }
};

const resetForm = () => {
  produk.produk = "";
  produk.kategori = "";
  produk.supplier = "";
  produk.stok = "";
  produk.harga = "";
};

const editDialog = ref(false);
const editedItem = reactive({});

const openEditDialog = async (item) => {
  try {
    const response = await axios.get(
      `http://127.0.0.1:8000/api/produk/${item.id}/edit`
    );
    editedItem.id = response.data.id;
    editedItem.produk = response.data.produk;
    editedItem.kategori = response.data.kategori;
    editedItem.supplier = response.data.supplier;
    editedItem.stok = response.data.stok;
    editedItem.harga = response.data.harga;
    editDialog.value = true;
  } catch (error) {
    console.error(error);
  }
};

const closeEditDialog = () => {
  editDialog.value = false;
};

const saveEdit = async () => {
  try {
    const response = await axios.put(
      `http://127.0.0.1:8000/api/produk/${editedItem.id}`,
      editedItem
    );
    console.log("Data Berhasil Diubah ====>", response.data);
    closeEditDialog();
    await fetchProducts();
  } catch (error) {
    console.error(error);
  }
};

const deleteDialog = ref(false);
const deleteItem = reactive({});

const openDeleteDialog = (item) => {
  deleteItem.id = item.id;
  deleteDialog.value = true;
};

const closeDeleteDialog = () => {
  deleteDialog.value = false;
};

const deleteItemConfirm = async () => {
  try {
    const response = await axios.delete(
      `http://127.0.0.1:8000/api/produk/${deleteItem.id}`
    );
    if (response.status === 204) {
      console.log("Data Berhasil Dihapus ====>", response);
    }
    closeDeleteDialog();
    await fetchProducts(); // Mengambil data produk terbaru setelah hapus
  } catch (error) {
    console.error(error);
  }
};

fetchProducts();
</script>

<template>
  <div>
    <v-card>
      <v-card-text>
        <v-row>
          <v-col cols="4" md="4">
            <p>Data Produk</p>
          </v-col>
          <v-col cols="4" md="4" class="text-center">
            <VDialog v-model="isDialogVisible" max-width="600">
              <!-- Dialog Activator -->
              <template #activator="{ props }">
                <VBtn v-bind="props"
                  ><VIcon icon="tabler-plus" /> Tambah Data
                </VBtn>
              </template>

              <!-- Dialog close btn -->
              <DialogCloseBtn @click="closeDialog" />

              <!-- Dialog Content -->
              <VForm @submit.prevent="submitForm">
                <VCard title="User Profile">
                  <VCardText>
                    <VRow>
                      <VCol cols="12">
                        <AppTextField
                          v-model="produk.produk"
                          label="Nama Produk"
                        />
                      </VCol>
                      <VCol cols="12">
                        <AppTextField
                          v-model="produk.kategori"
                          label="Kategori"
                        />
                      </VCol>
                      <VCol cols="12">
                        <AppTextField
                          v-model="produk.supplier"
                          label="Supplier"
                        />
                      </VCol>
                      <VCol cols="12" sm="6">
                        <AppTextField
                          v-model="produk.stok"
                          label="Stok"
                          type="number"
                        />
                      </VCol>
                      <VCol cols="12" sm="6">
                        <AppTextField
                          v-model="produk.harga"
                          label="harga"
                          type="number"
                        />
                      </VCol>
                    </VRow>
                  </VCardText>

                  <VCardText class="d-flex justify-end flex-wrap gap-3">
                    <VBtn color="secondary" outlined @click="closeDialog">
                      Close
                    </VBtn>
                    <VBtn outlined @click="submitForm"> Save </VBtn>
                  </VCardText>
                </VCard>
              </VForm>
            </VDialog>
          </v-col>
          <v-col cols="4" md="4">
            <v-text-field
              v-model="search"
              label="Search"
              dense
              outlined
            ></v-text-field>
          </v-col>
        </v-row>
      </v-card-text>

      <v-card-text>
        <v-data-table
          :headers="headers"
          :items="product"
          :search="search"
          :items-per-page="5"
          item-key="id"
        >
          <template #item.actions="{ item }">
            <div class="d-flex gap-1">
              <IconBtn @click="openEditDialog(item.raw)">
                <VIcon icon="mdi-pencil-outline" />
              </IconBtn>
              <IconBtn @click="openDeleteDialog(item.raw)">
                <VIcon icon="mdi-delete-outline" />
              </IconBtn>
            </div>
          </template>
        </v-data-table>
      </v-card-text>

      <!-- 👉 Edit Dialog  -->
      <VDialog v-model="editDialog" max-width="600px">
        <VCard>
          <VCardTitle>
            <span class="headline">Edit Item</span>
          </VCardTitle>

          <VCardText>
            Produk : {{ editedItem?.produk }} {{ editedItem?.id }}
            <VContainer>
              <VRow>
                <!-- full_name -->
                <VCol cols="12" sm="6" md="4">
                  <VTextField v-model="editedItem.produk" label="Nama Produk" />
                </VCol>

                <!-- email -->
                <VCol cols="12" sm="6" md="4">
                  <VTextField v-model="editedItem.kategori" label="Kategori" />
                </VCol>

                <!-- salary -->
                <VCol cols="12" sm="6" md="4">
                  <VTextField v-model="editedItem.supplier" label="Supplier" />
                </VCol>

                <!-- age -->
                <VCol cols="12" sm="6" md="4">
                  <VTextField
                    v-model="editedItem.stok"
                    label="Stok"
                    type="number"
                  />
                </VCol>

                <!-- start date -->
                <VCol cols="12" sm="6" md="4">
                  <VTextField v-model="editedItem.harga" label="Harga" />
                </VCol>

                <!-- status -->
              </VRow>
            </VContainer>
          </VCardText>

          <VCardActions>
            <VSpacer />

            <VBtn color="error" variant="outlined" @click="closeEditDialog">
              Cancel
            </VBtn>

            <VBtn color="success" variant="elevated" @click="saveEdit">
              Save
            </VBtn>
          </VCardActions>
        </VCard>
      </VDialog>

      <!-- 👉 Delete Dialog  -->
      <VDialog v-model="deleteDialog" max-width="500px">
        <VCard>
          <VCardTitle> Are you sure you want to delete this item? </VCardTitle>

          <VCardActions>
            <VSpacer />

            <VBtn color="error" variant="outlined" @click="closeDeleteDialog">
              Cancel
            </VBtn>

            <VBtn color="success" variant="elevated" @click="deleteItemConfirm">
              OK
            </VBtn>

            <VSpacer />
          </VCardActions>
        </VCard>
      </VDialog>
    </v-card>
  </div>
</template>
