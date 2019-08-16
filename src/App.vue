<template>
  <div id="app">
    <b-navbar toggleable="md" type="dark" class="nav-background">
      <b-navbar-brand href="#">Detail Domain resolve</b-navbar-brand>
    </b-navbar>
    <b-container>
      <b-row align-h="center" class="mt-3">
        <b-col>
          <b-card header="Search">
            <b-form @reset="onReset"  v-if="show">
              <b-form-group id="input-group-2" label="Domain:" label-for="input-2">
                <b-form-input
                  id="input-2"
                  v-model="form.domain"
                  required
                  placeholder="Enter domain"
                ></b-form-input>
              </b-form-group>
              <b-row align-h="center" class="mt-5">
                
                <div class="d-flex justify-content-between">
                  <div>
                    <b-button
                      class="btn-space"
                      variant="primary"
                      @click="searchByDomain"
                    >Search by domain</b-button>
                    <b-button type="reset" class="btn-space" variant="danger">Reset</b-button>
                    <b-button
                      class="btn-space"
                      variant="primary"
                      @click="searchHistory"
                    >Search history</b-button>
                  </div>
                  <div>
                  <b-spinner label="Loading..." v-if="showLoading"></b-spinner>
                </div>  
                </div>
              </b-row>
            </b-form>
          </b-card>
        </b-col>
      </b-row>
      <b-row align-h="center" class="mt-3">
        <b-col>
          <b-card header="Result">
            <b-table striped hover :items="items"></b-table>
          </b-card>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
export default {
  data() {
    return {
      form: {
        domain: ""
      },
      show: true,
      showLoading: false,
      items: []
    };
  },
  methods: {
    searchHistory() {
      showLoading:true;
      fetch("http://localhost:3000/api/v1/search-by-domain/history")
        .then(response => response.json())
        .then(result => {
          this.processResponse(result.data);
        });
    },
    searchByDomain() {
      fetch("http://localhost:3000/api/v1/search-by-domain/" + this.form.domain)
        .then(response => response.json())
        .then(result => {
          this.processResponse([result.data]);
        });
    },

    processResponse(result) {
      this.items = [];
      result.forEach(item => {
        let element = {
          host: item.host,
          servers_changed: item.servers_changed,
          logo: item.logo,
          is_down: item.is_down,
          title: item.title,
          servers: ""
        };
        if (item.servers) {
          item.servers.forEach(subitem => {
            element.servers =
              element.servers +
              " " +
              subitem.address +
              ", " +
              subitem.ssl_grade +
              ", " +
              subitem.country +
              ", " +
              subitem.owner +
              " ";
          });
        }

        this.items.push(element);
      });
    },
    onReset(evt) {
      evt.preventDefault();
      // Reset our form values
      this.form.domain = "";
      this.show = false;
      this.$nextTick(() => {
        this.show = true;
      });
    }
  }
};
</script>

<style>
@import url("https://fonts.googleapis.com/css?family=Lato:400,700");
body {
  background: #eef1f4 !important;
  font-family: "Lato", sans-serif !important;
}
.nav-background {
  background: #353535;
}
.btn-space {
  margin-right: 5px;
}
</style>
