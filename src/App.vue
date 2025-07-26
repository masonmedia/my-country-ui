<template>
  <div class="container px-5 vh-100 overflow-hidden d-flex flex-column">
    <!-- <div class="row mb-2 px-3 pt-3">
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
    </div> -->

    <div class="row mb-2 px-3 pt-3">
  <!-- First: dynamically generated buttons -->
  <div
    v-for="country in countryList"
    :key="country"
    class="col-12 col-md-3 mb-2"
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

  <!-- Then: 4th dropdown button -->
  <div class="col-12 col-md-3 mb-2">
    <div class="dropdown w-100">
      <button
        class="btn w-100 fw-bold border-0 p-3 rounded-4 d-flex align-items-center justify-content-between"
        :style="{ backgroundColor: 'lightgrey' }"
        data-bs-toggle="dropdown"
        aria-expanded="false"
      >
        <span class="d-flex align-items-center">
          <img
            v-if="countryData?.flags?.png"
            :src="countryData.flags.png"
            alt="Flag"
            style="width: 24px; height: 16px; object-fit: cover; margin-right: 8px;"
          />
          Countries
        </span>
        <span class="dropdown-toggle"></span>
      </button>
      <ul class="dropdown-menu w-100 mt-2" style="max-height: 300px; overflow-y: auto;">
        <!-- Add search input at the top -->
        <li class="px-3 py-2 border-bottom">
          <input
            type="text"
            class="form-control form-control-sm"
            placeholder="Type to search countries..."
            v-model="searchQuery"
            @input="filterCountries"
            @keydown.esc="clearSearch"
          />
        </li>
        
        <li v-if="filteredCountries.length === 0 && searchQuery">
          <span class="dropdown-item text-muted">No countries found</span>
        </li>
        
        <li v-if="filteredCountries.length === 0 && !searchQuery && allCountries.length === 0">
          <span class="dropdown-item">Loading countries...</span>
        </li>
        
        <li
          v-for="country in filteredCountries"
          :key="country.cca3"
        >
          <button
            class="dropdown-item d-flex align-items-center"
            @click="selectCountryFromDropdown(country.name.common)"
          >
            <img
              v-if="country.flags?.png"
              :src="country.flags.png"
              alt="Flag"
              style="width: 20px; height: 14px; object-fit: cover; margin-right: 8px;"
            />
            {{ country.name.common }}
          </button>
        </li>
      </ul>
    </div>
  </div>
</div>


    <div class="row g-3 flex-grow-1 px-3 pb-3">
        <!-- First row: Country name (7/12) and Currency (5/12) -->
        <div class="col-lg-7 col-md-6 col-sm-12">
          <div class="bg-dark text-white rounded-4 h-100 d-flex flex-column justify-content-center align-items-center">
            <div v-if="loading && !countryData" class="text-center">
              <div class="spinner-border text-light" role="status">
                <span class="visually-hidden">Loading...</span>
              </div>
            </div>
            <h1 v-else-if="countryData" class="text-center fw-bold" style="font-size: 12vmin; line-height: 1; word-break: break-word;">{{ countryData.name.common.toUpperCase() }}</h1>
            <div v-else class="text-center">
              <div class="spinner-border text-light" role="status">
                <span class="visually-hidden">Loading...</span>
              </div>
            </div>
          </div>
        </div>
        <div class="col-lg-5 col-md-6 col-sm-12">
          <div style="background: lightgrey" class="rounded-4 h-100 d-flex flex-column justify-content-center align-items-center text-center">
            <div v-if="loading && !countryData" class="text-center">
              <div class="spinner-border text-secondary" role="status">
                <span class="visually-hidden">Loading...</span>
              </div>
            </div>
            <div v-else-if="countryData">
              <!-- <h4>Currency</h4> -->
              <h4 class="fw-bold display-1">$</h4>
              <div v-for="(currency, code) in countryData.currencies" :key="code">
                <p class="fw-bold h3">{{ currency.name.toUpperCase() }}</p>
              </div>
            </div>
            <div v-else class="text-center">
              <div class="spinner-border text-secondary" role="status">
                <span class="visually-hidden">Loading...</span>
              </div>
            </div>
          </div>
        </div>

        <!-- Second row: Coat of Arms (5/12) and Flag (7/12) -->
        <div class="col-lg-5 col-md-6 col-sm-12">
          <div style="background: lightgrey" class="rounded-4 h-100 d-flex flex-column justify-content-center align-items-center text-center">
            <div v-if="loading && !countryData" class="text-center">
              <div class="spinner-border text-secondary" role="status">
                <span class="visually-hidden">Loading...</span>
              </div>
            </div>
            <div v-else-if="countryData">
              <!-- <h4>Coat of Arms</h4> -->
              <img :src="countryData.coatOfArms?.png" alt="Coat of Arms" class="" style="height: 300px; width: 300px" v-if="countryData.coatOfArms?.png">
              <p v-else>No Coat of Arms Available</p>
            </div>
            <div v-else class="text-center">
              <div class="spinner-border text-secondary" role="status">
                <span class="visually-hidden">Loading...</span>
              </div>
            </div>
          </div>
        </div>
        <div class="col-lg-7 col-md-6 col-sm-12">
          <div class="bg-light rounded-4 h-100 position-relative">
            <div v-if="loading && !countryData" class="text-center h-100 d-flex align-items-center justify-content-center">
              <div class="spinner-border text-secondary" role="status">
                <span class="visually-hidden">Loading...</span>
              </div>
            </div>
            <div v-else-if="countryData">
              <img :src="countryData.flags?.png" alt="Flag" class="img-flag rounded-4" v-if="countryData.flags?.png">
              <p v-else class="text-center h-100 d-flex align-items-center justify-content-center">No Flag Available</p>
            </div>
            <div v-else class="text-center h-100 d-flex align-items-center justify-content-center">
              <div class="spinner-border text-secondary" role="status">
                <span class="visually-hidden">Loading...</span>
              </div>
            </div>
          </div>
        </div>
      </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      countryList: ["Canada", "Thailand", "Spain"],
      selectedCountry: "Canada",
      countryData: null,
      loading: false,
      allCountries: [],
      searchQuery: '', // Add this
      filteredCountries: [] // Add this
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
    },
    async fetchAllCountries() {
      try {
        console.log('Fetching all countries...');
        const res = await fetch('https://restcountries.com/v3.1/all?fields=name,flags,cca3');
        const data = await res.json();
        console.log('API response type:', typeof data);
        console.log('API response:', data);
        
        // Check if data is an array
        if (!Array.isArray(data)) {
          console.error('API response is not an array:', data);
          return;
        }
        
        console.log('Countries fetched:', data.length);
        // Sort countries alphabetically by name
        this.allCountries = data.sort((a, b) => a.name.common.localeCompare(b.name.common));
        console.log('First few countries:', this.allCountries.slice(0, 5));
      } catch (err) {
        console.error("Error fetching all countries:", err);
      }
    },
    selectCountryFromDropdown(countryName) {
      this.fetchCountryData(countryName);
    },
    filterCountries() {
      if (!this.searchQuery.trim()) {
        this.filteredCountries = this.allCountries;
        return;
      }
      
      const query = this.searchQuery.toLowerCase();
      this.filteredCountries = this.allCountries.filter(country => 
        country.name.common.toLowerCase().startsWith(query)
      );
    },
    clearSearch() {
      this.searchQuery = '';
      this.filterCountries();
    }
  },
  mounted() {
    this.fetchCountryData(this.selectedCountry);
    this.fetchAllCountries();
  },
  watch: {
    allCountries: {
      handler(newCountries) {
        this.filteredCountries = newCountries;
      },
      immediate: true
    }
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

/* .img-flag {
  width: 100%;
  height: 100%;
  min-height: 100%;
  object-fit: cover;
  border-radius: 1rem;
  display: block;
  margin: auto;
} */

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

.dropdown-menu {
  border-radius: 1rem;
  border: none;
  box-shadow: 0 0.5rem 1rem rgba(0, 0, 0, 0.15);
}

.dropdown-item {
  padding: 0.5rem 1rem;
  border-radius: 0.5rem;
  margin: 0.25rem;
}

.dropdown-item:hover {
  background-color: #f8f9fa;
}

.dropdown-item:active {
  background-color: #e9ecef;
}
</style>
