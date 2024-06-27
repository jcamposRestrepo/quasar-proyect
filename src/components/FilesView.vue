<template>
  <h5>Archivos</h5>
  <div class="row">
    <div class="col-2 row bi-border cursor" v-for="item in dataFiles" :key="item">
      <div v-bind:class="[item.anchor === false ? 'anchor-no' : 'anchor']">
        <span class="material-icons q-mx-md" @click="ancharFile(item,item.anchor)">
            anchor
          </span>
      </div>
      <div class="col-12 text-center q-pa-md" @click="viewmodal(item)">
        <q-img :src="item.urlFile !== '' && (item.file.type === 'image/png' || item.file.type === 'image/jpeg') ?
        item.urlFile : 'src/assets/img/file.png'"
               spinner-color="white" style="height: 140px; max-width: 150px" />

      </div>
      <div class="col-12 text-center">{{ item.nameFile }}</div>
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

    return {pdfUrl,dialog,register,$q}
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
