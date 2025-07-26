<template>
  <div class="container-fluid vh-100 overflow-hidden d-flex flex-column">
    <div class="row mb-2 px-3 pt-3">
      <div
        v-for="country in countryList"
        :key="country"
        class="col-12 col-md-4 mb-2"
      >
        <button
          class="btn w-100 fw-bold border-0 p-3 rounded-4"
          :class="{ 'bg-dark text-white': selectedCountry === country }"
          :style="{ backgroundColor: selectedCountry !== country ? 'lightgrey' : '' }"
          @click="fetchCountryData(country)"
        >
          {{ country.toUpperCase() }}
        </button>
      </div>
    </div>

    <transition name="fade">
      <div v-if="countryData || loading" class="row g-3 flex-grow-1 px-3 pb-3">
        <!-- First row: Country name (7/12) and Currency (5/12) -->
        <div class="col-md-7 col-sm-12">
          <div class="bg-dark text-white rounded-4 h-100 d-flex flex-column justify-content-center align-items-center">
            <div v-if="loading" class="text-center">
              <div class="spinner-border text-light" role="status">
                <span class="visually-hidden">Loading...</span>
              </div>
            </div>
            <h1 v-else class="text-center" style="font-size: 12vmin; line-height: 1; word-break: break-word;">{{ countryData.name.common.toUpperCase() }}</h1>
          </div>
        </div>
        <div class="col-md-5 col-sm-12">
          <div style="background: lightgrey" class="rounded-4 h-100 d-flex flex-column justify-content-center align-items-center text-center">
            <div v-if="loading" class="text-center">
              <div class="spinner-border text-secondary" role="status">
                <span class="visually-hidden">Loading...</span>
              </div>
            </div>
            <div v-else>
              <!-- <h4>Currency</h4> -->
              <h4 class="fw-bold display-1">$</h4>
              <div v-for="(currency, code) in countryData.currencies" :key="code">
                <p class="fw-bold h3">{{ currency.name.toUpperCase() }}</p>
              </div>
            </div>
          </div>
        </div>

        <!-- Second row: Coat of Arms (5/12) and Flag (7/12) -->
        <div class="col-md-5 col-sm-12">
          <div style="background: lightgrey" class="rounded-4 h-100 d-flex flex-column justify-content-center align-items-center text-center">
            <div v-if="loading" class="text-center">
              <div class="spinner-border text-secondary" role="status">
                <span class="visually-hidden">Loading...</span>
              </div>
            </div>
            <div v-else>
              <!-- <h4>Coat of Arms</h4> -->
              <img :src="countryData.coatOfArms?.png" alt="Coat of Arms" class="img-fluid" v-if="countryData.coatOfArms?.png">
              <p v-else>No Coat of Arms Available</p>
            </div>
          </div>
        </div>
        <div class="col-md-7 col-sm-12">
          <div class="bg-light rounded-4 h-100 position-relative">
            <div v-if="loading" class="text-center h-100 d-flex align-items-center justify-content-center">
              <div class="spinner-border text-secondary" role="status">
                <span class="visually-hidden">Loading...</span>
              </div>
            </div>
            <div v-else>
              <img :src="countryData.flags?.png" alt="Flag" class="img-flag rounded-4" v-if="countryData.flags?.png">
              <p v-else class="text-center h-100 d-flex align-items-center justify-content-center">No Flag Available</p>
            </div>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
export default {
  data() {
    return {
      countryList: ["Canada", "Thailand", "Spain"],
      selectedCountry: "Canada",
      countryData: null,
      loading: false
    };
  },
  methods: {
    async fetchCountryData(name) {
      this.selectedCountry = name;
      this.loading = true;
      try {
        const res = await fetch(`https://restcountries.com/v3.1/name/${name}?fullText=true`);
        const data = await res.json();
        this.countryData = data[0];
      } catch (err) {
        console.error("Error fetching data:", err);
      } finally {
        this.loading = false;
      }
    }
  },
  mounted() {
    this.fetchCountryData(this.selectedCountry);
  }
};
</script>

<style>
button.active {
  background-color: black;
  color: white;
  font-weight: bold;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter, .fade-leave-to {
  opacity: 0;
}

img {
  max-height: 150px;
  object-fit: contain;
}

.img-flag {
  width: 100% !important;
  height: 100% !important;
  max-height: none !important;
  object-fit: cover !important;
  display: block !important;
  margin: 0 !important;
  padding: 0 !important;
  position: absolute !important;
  top: 0 !important;
  left: 0 !important;
}
</style>
