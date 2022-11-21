<script setup>
import { computed, reactive, ref, watchEffect } from "vue";

//
const mama = reactive({
  data: JSON.parse(localStorage.getItem("mamapulsa") || "[]"),
  form: {
    provider: null,
    nomorHp: "",
    nominal: null,
    status: "",
    catatan: "",
  },
  filter: {
    provider: "",
    status: "",
    cari: null,
  },
  selected: null,
  mode: "add",
  search: null,
});

watchEffect(() => {
  localStorage.setItem("mamapulsa", JSON.stringify(mama.data));
});

const customStatus = (status) => {
  switch (status) {
    case "SUCCESS":
      return " text-bg-success ";
    case "PENDING":
      return " text-bg-warning ";
    case "CANCELLED":
      return " text-bg-danger ";
    default:
      return " text-bg-dark ";
  }
};

const initForm = () => {
  (mama.form = {
    provider: null,
    nomorHp: null,
    nominal: null,
    catatan: null,
  }),
    (mama.mode = "add"),
    (mama.selected = {});
};

const filterData = computed(() => {
  return mama.data.filter((item) => {
    if (mama.filter.provider) {
      if (item.provider !== mama.filter.provider) {
        return false;
      }
    }
    if (mama.filter.status) {
      if (item.status !== mama.filter.status) {
        return false;
      }
    }
    if (mama.filter.cari) {
      if (!item.nomorHp.startsWith(mama.filter.cari)) {
        return false;
      }
    }
    return true;
  });
});

function createTodo() {
  if (mama.mode === "add") {
    mama.data.unshift({
      id: Math.random(),
      tanggal: new Date().toISOString(),
      provider: mama.form.provider,
      nomorHp: mama.form.nomorHp,
      nominal: mama.form.nominal,
      status: "PENDING",
      catatan: mama.form.catatan,
    });

    initForm();
  } else {
    const i = mama.data.findIndex((o) => o.id === mama.selected.id);
    mama.data[i] = {
      ...mama.selected,
      provider: mama.form.provider,
      nomorHp: mama.form.nomorHp,
      nominal: mama.form.nominal,
      catatan: mama.form.catatan,
      status: mama.form.status,
    };
    // Reset
    initForm();
  }
}

function delMama(idx) {
  mama.data.splice(idx, 1);
}
</script>

<template>
  <div class="container">
    <h1>Mama Pulsa</h1>
    <h4 class="mb-4">Mama Pulsa</h4>

    <div class="row">
      <!-- =================== colom inputan =============== -->
      <div class="col-3 border rounded p-2">
        <h5 class="mt-3">📲 Beli Pulsa</h5>
        <hr />
        <form @submit.prevent="createTodo">
          <select
            class="form-select mb-3"
            v-model="mama.form.provider"
            aria-label="Default select example"
          >
            <option :value="null">Pilih Provider</option>
            <option value="TELKOMSEL">TELKOMSEL</option>
            <option value="XL">XL</option>
            <option value="IM3">IM3</option>
          </select>
          <h6>Nomor Hp</h6>
          <input
            class="form-control mb-3"
            v-model="mama.form.nomorHp"
            placeholder="Masukkan Nomor. HP"
            type="text"
            aria-label="default input example"
          />
          <h6>Nominal</h6>
          <input
            class="form-control mb-3"
            v-model="mama.form.nominal"
            placeholder="Masukkan Nominal Pulsa"
            type="number"
            aria-label="default input example"
          />
          <h6>Catatan</h6>
          <textarea
            class="form-control mb-3"
            v-model="mama.form.catatan"
            placeholder="Masukkan Catatan"
            id="exampleFormControlTextarea1"
            rows="3"
          ></textarea>

          <div v-if="mama.mode === 'edit'" class="row mb-3">
            <h6>Status</h6>
            <div class="col">
              <label class="form-check-label me-1" for="flexRadioDefault1"
                >Pending</label
              >
              <input
                class="form-check-input"
                type="radio"
                name="flexRadioDefault"
                id="flexRadioDefault1"
                value="PENDING"
                v-model="mama.form.status"
              />
            </div>
            <div class="col">
              <label class="form-check-label me-1" for="flexRadioDefault1"
                >Success
              </label>
              <input
                class="form-check-input"
                type="radio"
                name="flexRadioDefault"
                id="flexRadioDefault1"
                value="SUCCESS"
                v-model="mama.form.status"
              />
            </div>
            <div class="col">
              <label class="form-check-label me-1" for="flexRadioDefault1"
                >Canceled
              </label>
              <input
                class="form-check-input"
                type="radio"
                name="flexRadioDefault"
                id="flexRadioDefault1"
                value="CANCELLED"
                v-model="mama.form.status"
              />
            </div>
          </div>

          <button type="submit" class="btn btn-primary me-3">
            {{ mama.mode === "add" ? "Simpan" : "Edit" }}
          </button>
          <button
            v-if="mama.mode === 'edit'"
            @click="initForm"
            class="btn btn-danger"
          >
            Batal
          </button>
        </form>
      </div>

      <!-- ============== colom data =================== -->
      <div class="col-8 ms-4">
        <div class="row">
          <div class="col">
            <input
              type="text"
              class="form-control"
              placeholder="Cari"
              aria-label="First name"
              v-model="mama.filter.cari"
            />
          </div>
          <div class="col">
            <select
              class="form-select mb-3"
              aria-label="Default select example"
              v-model="mama.filter.provider"
            >
              <option :value="''">Filter by Provider</option>
              <option value="TELKOMSEL">TELKOMSEL</option>
              <option value="XL">XL</option>
              <option value="IM3">IM3</option>
            </select>
          </div>
          <div class="col">
            <select
              class="form-select mb-3"
              aria-label="Default select example"
              v-model="mama.filter.status"
            >
              <option :value="''">Filter by Status</option>
              <option value="PENDING">PENDING</option>
              <option value="SUCCESS">SUCCESS</option>
              <option value="CANCELED">CANCELED</option>
            </select>
          </div>
          <div class="col">
            <button
              @click="
                mama.filter.cari = '';
                mama.filter.provider = '';
                mama.filter.status = '';
              "
              v-if="
                mama.filter.cari || mama.filter.provider || mama.filter.status
              "
              class="btn btn-dark"
            >
              Reset
            </button>
          </div>
        </div>
        <table class="table border-top">
          <thead>
            <tr>
              <th scope="col">DATE</th>
              <th scope="col">PROVIDER</th>
              <th scope="col">NOMOR HP</th>
              <th scope="col">NOMINAL</th>
              <th scope="col">STATUS</th>
              <th scope="col">CATATAN</th>
              <th scope="col">ACTION</th>
            </tr>
          </thead>
          <tbody v-if="filterData.length">
            <tr v-for="(item, index) in filterData" :key="item.id">
              <td>{{ item.tanggal }}</td>
              <td>{{ item.provider }}</td>
              <td>{{ item.nomorHp }}</td>
              <td>Rp. {{ item.nominal }}</td>
              <td>
                <span :class="customStatus(item.status) + ' badge '">
                  {{ item.status }}
                </span>
              </td>
              <td>{{ item.catatan }}</td>
              <td>
                <button
                  type="button"
                  @click="
                    mama.mode = 'edit';
                    mama.selected = item;
                    mama.form = { ...item };
                  "
                  class="btn btn-success mx-1"
                >
                  ✍
                </button>
                <button
                  type="button"
                  @click="delMama(index)"
                  class="btn btn-danger"
                >
                  ❌
                </button>
              </td>
            </tr>
          </tbody>

          <tbody v-else>
            <tr>
              <td>
                <span>data kosong</span>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<style>
* {
  font-size: 0.9rem;
}

.container {
  margin-top: 1rem;
}
</style>
