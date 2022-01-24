<template>
  <div class="container">
    <div class="text-right mb-3">
      <b-button @click="logout()" variant="danger" class="mr-3">Cerrar sesion</b-button>
    </div>
    <b-card
      border-variant="info"
      :header="msg"
      header-bg-variant="info"
      header-text-variant="white"
      align="left"
    >
      <b-card-text>
        <b-form @submit="onSubmit" v-if="show">
          <b-row>
            <b-form-group
              id="input-group-1"
              label="Nombres y Apellidos"
              label-for="input-1"
              class="col-md-3"
            >
              <b-form-input
                id="input-1"
                v-model="form.name"
                type="text"
                placeholder="Ej. Alejandro Hernandez"
                required
              ></b-form-input>
            </b-form-group>

            <b-form-group id="input-group-2" label="Porcentaje coincidencia" label-for="input-2" class="col-md-3">
              <b-form-input
                id="input-2"
                v-model="form.percentage"
                type="number"
                placeholder="Ej. 95"
                required
              ></b-form-input>
            </b-form-group>

            <b-col class="d-flex align-items-center">
              <div class="mt-md-3">
                <b-button type="submit" variant="info" class="mr-3">Buscar</b-button>
                <b-button v-if="items.length > 0" variant="primary">Exportar</b-button>
              </div>
            </b-col>
          </b-row>
        </b-form>
        <div class="mt-3">
          <b-table
            striped
            hover
            responsive
            :items="items"
            :fields="fields"
            :per-page="perPage"
            :current-page="currentPage"
          ></b-table>
        </div>
        <div v-if="items.length > 0">
          <b-pagination
            v-model="currentPage"
            :total-rows="rows"
            :per-page="perPage"
            aria-controls="my-table"
            align="right"
            variant="info"
          ></b-pagination>
        </div>
      </b-card-text>
    </b-card>
  </div>
</template>

<script>
export default {
  name: 'index',
  props: {
    msg: String
  },
  mounted() {
    if (localStorage.getItem('token')) {
      console.log(localStorage.getItem('token'))
    } else {
      this.$router.push('/login')
    }
  },
  data() {
    return {
      form: {
        name: '',
        percentage: 0
      },
      show: true,
      state: false,
      fields: [
        {
          key: 'name',
          label: 'Nombre',
          sortable: true
        },
        {
          key: 'person_type',
          label: 'Tipo persona',
          sortable: false
        },
        {
          key: 'charge_type',
          label: 'Tipo cargo',
          sortable: true,
        },
        {
          key: 'department',
          label: 'Departamento',
          sortable: true,
        },
        {
          key: 'municipality',
          label: 'Municipio',
          sortable: true,
        },
        {
          key: 'coincidence',
          label: '% Coincidencia',
          sortable: true,
        }
      ],
      perPage: 10,
      currentPage: 1,
      items: []
    }
  },
  methods: {
    onSubmit(event) {
      event.preventDefault()
      let me = this
      this.axios.get('data', {
        params: me.form
      }).then((response) => {
        me.items = response.data
        me.items.sort(function (a, b) {
          if (a.coincidence < b.coincidence) {
            return 1;
          }
          if (a.coincidence > b.coincidence) {
            return -1;
          }
          // a must be equal to b
          return 0;
        });
        if(response.data.length > 0){
          this.$swal({
            toast: true,
            position: 'top-end',
            icon: 'success',
            title: 'Exitoso con resultados',
            showConfirmButton: false,
            timer: 2000
          });
        } else {
          this.$swal({
            toast: true,
            position: 'top-end',
            icon: 'warning',
            title: 'Exitoso sin resultados',
            showConfirmButton: false,
            timer: 2000
          });
        }
      })
    },
    logout() {
      localStorage.removeItem('token');
      this.$router.push('/login')
    }
  },
  computed: {
    rows() {
      return this.items.length
    }
  }
}
</script>


