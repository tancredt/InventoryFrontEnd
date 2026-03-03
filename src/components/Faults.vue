<template>
  <div>
    <!-- Your Faults template code -->
  </div>
</template>

<script>
export default {
  data() {
    return {
      faults: [],
      loading: false,
      filters: {},
      page: 1,
      // other data properties
    };
  },
  watch: {
    filters: {
      handler(newFilters) {
        this.fetchFaults(newFilters);
      },
      deep: true,
    },
  },
  methods: {
    fetchFaults(filters) {
      this.loading = true;
      // Implement filtering on backend
      const filterParams = this.extractFilterParams(filters);
      // Call the backend API with only filterParams
      // e.g., axios.get('/api/faults', { params: filterParams })
      this.$http.get('/api/faults', { params: filterParams })
        .then(response => {
          this.faults = response.data;
        })
        .finally(() => {
          this.loading = false;
        });
    },
    extractFilterParams(filters) {
      // Logic to extract filter parameters
      return filters; // Example: return relevant filters
    },
    // Remove all frontend filtering logic from performFilteringAndCalculateTotals
    // Modify this method accordingly
    // Keep your sorting and pagination logic here
  },
};
</script>
