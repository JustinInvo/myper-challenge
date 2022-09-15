<template>
  <div class="container">
    <h1 class="mb-4">Listado <strong style="color:#42b983;">Giblin</strong></h1>

    <b-col class="my-1">
      <div class="d-flex justify-content-between">
        <b-form-group
        label="Filtrar"
        label-for="filter-input"
        label-cols-sm="3"
        label-align-sm="right"
        label-size="sm"
        class="mb-0"
      >
        <b-input-group size="sm">
          <b-form-input
            id="filter-input"
            v-model="filter"
            type="search"
            placeholder="escribe para buscar"
          ></b-form-input>

          <b-input-group-append>
            <b-button :disabled="!filter" @click="filter = ''">Limpiar</b-button>
          </b-input-group-append>
        </b-input-group>
      </b-form-group>
      <b-button variant="success" @click="Listar">
        Actualizar
        <b-icon icon="arrow-clockwise" animation="spin-pulse" font-scale="4" style="width:15px;height:15px;"></b-icon>
      </b-button>
      </div>
    </b-col>
      <h4 class="my-4">Numero de pagina actual: <span style="color:#42b983;">{{ currentPage }}</span></h4>
  
      <b-table
        id="my-table"
        :items="items"
        :per-page="perPage"
        :current-page="currentPage"
        :fields="fields"
        :filter="filter"
        small
        :busy="isBusy"
        show-empty
        empty-text="No se encontraron resultados"
        empty-filtered-text="No se encontraron resultados"
      >
      <template #table-busy>
        <div class="text-center text-success my-2">
            <b-spinner class="align-middle"></b-spinner>
            <strong> Cargando...</strong>
        </div>
      </template>
        <template #cell(image)="data">
          <img width="60" :src="data.item.image" alt="">
        </template>
        <template #empty="scope">
          <h4>{{ scope.emptyText }}</h4>
        </template>
        <template #emptyfiltered="scope">
          <h4>{{ scope.emptyFilteredText }}</h4>
        </template>
      </b-table>
    <b-pagination
      v-model="currentPage"
      :total-rows="rows"
      :per-page="perPage"
      class="mt-4"
    >
      <template #first-text><span class="text-success">First</span></template>
      <template #prev-text><span class="text-danger">Prev</span></template>
      <template #next-text><span class="text-warning">Next</span></template>
      <template #last-text><span class="text-info">Last</span></template>
      <template #ellipsis-text>
        <b-spinner small type="grow"></b-spinner>
        <b-spinner small type="grow"></b-spinner>
        <b-spinner small type="grow"></b-spinner>
      </template>
      <template #page="{ page, active }">
        <b v-if="active">{{ page }}</b>
        <i v-else>{{ page }}</i>
      </template>
    </b-pagination>
  </div>
</template>

<script>
export default {
  name: 'Table',
  data() {
    return {
      filter: null,
      perPage: 5,
      currentPage: 1,
      items: [],
      isBusy: false,
      fields: [
        {
          key: 'image',
          label: 'Imagen',
        },
        {
          key: 'title',
          label: 'Título',
          sortable: true
        },
        {
          key: 'director',
          label: 'Director',
          sortable: true,
        },
        {
          key: 'release_date',
          label: 'Año',
          sortable: true
        },
        {
          key: 'rt_score',
          label: 'Puntaje',
          sortable: false
        }
      ],
    }
  },
  computed: {
    rows() {
      return this.items.length
    }
  },
  methods: {
    //se rellena la tabla
    Listar() {
      this.isBusy=true;
      this.axios.get('https://ghibliapi.herokuapp.com/films')
        .then(res => {
          const response = res.data;
          console.log(response)
          this.items = response;
          this.currentPage=1;
          this.filter = ''
          this.isBusy=false;
        })
        .catch(error => {
          console.log(error)
          this.isBusy=false;
        })
    }
  },
  created() {
    this.Listar();
  }
}
</script>