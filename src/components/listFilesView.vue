<template>
  <div class="row">
    <div class="col-12">
      <q-table
        title="Archivos"
        :rows="dataFiles"
        :columns="columns"
        row-key="name"
      >
        <template v-slot:body="props">
          <q-tr :props="props" @click="viewmodal(props.row)">
            <q-td key="anchor" :props="props">
              <div v-bind:class="[props.row.anchor === false ? 'anchor-no-list' : 'anchor-list']">
        <span class="material-icons q-mx-md" @click="ancharFile(props.row,props.row.anchor)">
            anchor
          </span>
              </div>
            </q-td>
            <q-td key="img" :props="props">
              <q-img :src="props.row.urlFile !== '' && (props.row.file.type === 'image/png' || props.row.file.type === 'image/jpeg') ?
        props.row.urlFile : 'src/assets/img/file.png'"
                     spinner-color="white" />
            </q-td>
            <q-td key="identify" :props="props">
                {{ props.row.identificationDocumentNumber }}
            </q-td>
            <q-td key="firstName" :props="props">
                {{ props.row.firstName }}
            </q-td>
            <q-td key="lastName" :props="props">
                {{ props.row.lastName }}
            </q-td>
            <q-td key="nameFile" :props="props">
                {{ props.row.nameFile }}
            </q-td>
            <q-td key="amountPages" :props="props">
                {{ props.row.amountPages }}
            </q-td>
            <q-td key="number" :props="props">
                {{ props.row.number }}
            </q-td>
          </q-tr>
        </template>
      </q-table>
    </div>

  </div>

  <!--  modal-->
  <q-dialog v-model="dialog" backdrop-filter="blur(4px)">
    <q-card style="width: auto; max-width: max-content;">
      <q-card-section class="row">
        <div class="col-4 text-h6">Vista Previa</div>
        <div class="text-center text-h5">
          <span class="material-icons md-36 icon" @click="edit">
            border_color
          </span>
          <span class="material-icons q-mx-md md-36 icon" @click="deleteFile">
            delete
          </span>
        </div>
        <q-space />
        <q-btn icon="close" flat round dense v-close-popup />
      </q-card-section>

      <q-card-section class="q-pt-none text-center">
        <object :data="pdfUrl" type="application/pdf" style="height: 700px; width: 1100px;"></object>
      </q-card-section>

      <q-card-actions align="right">
        <q-btn flat label="Cerrar" color="primary" v-close-popup />
      </q-card-actions>
    </q-card>
  </q-dialog>
</template>

<script>
import {ref} from "vue";
import {useQuasar} from "quasar";


export default {
  props:{
    dataFiles:{
      type: Array,
      required: true,
    },
  },
  setup(props){
    const pdfUrl = ref('');
    const dialog = ref(false)
    const register = ref(null)
    const $q = useQuasar()
    const columns = [
      { name: 'anchor', align: 'center', label: 'Anclar', field: 'anchor', sortable: true },
      { name: 'img', align: 'center', label: 'Adjunto', field: 'urlFile', sortable: true },
      { name: 'identify', align: 'center', label: 'N° identificación', field: 'identificationDocumentNumber', sortable: true },
      { name: 'firstName', align: 'center', label: 'Nombre', field: 'firstName', sortable: true },
      { name: 'lastName', align: 'center', label: 'Apellido', field: 'lastName', sortable: true },
      { name: 'nameFile', align: 'center', label: 'Nombre Archivo', field: 'nameFile', sortable: true },
      { name: 'amountPages', align: 'center', label: 'N° paginas', field: 'amountPages', sortable: true },
      { name: 'number', align: 'center', label: 'N°', field: 'number', sortable: true },
    ]

    return {pdfUrl,dialog,register,$q,columns}
  },
  methods: {
    edit() {
      this.$emit('edit', this.register );
      this.dialog = false;
    },
    deleteFile(){
      if (this.register.anchor === true){
        this.$q.notify({
          type: 'info',
          message: 'No puedes Eliminar archivo Anclado'
        })
      }else {
        this.$emit('delete', this.register );
        this.dialog = false;
      }
    },
    viewmodal(item){
      this.dialog = true
      this.pdfUrl = item.urlFile
      this.register = item

    },
    ancharFile(item,anchor){
      this.$emit('anchor', item, !anchor );
    }
  }
}

</script>
