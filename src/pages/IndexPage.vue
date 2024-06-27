<template>
  <div class="container q-pa-md">
      <div class="row">
        <div class="col-12">
          <q-expansion-item expand-separator icon="receipt" label="Adjuntar Archivo"
            v-model="extensForm" header-class="bg-teal text-white"  expand-icon-class="text-white">
            <q-card>
              <q-card-section>
                <div class="row">
                  <div class="col-6 q-pa-md">
                    <q-input rounded outlined v-model="idDocumentNumber" @change="filterNumberIdentification(idDocumentNumber)" type="number" label="*Numero identificación" />
                  </div>
                  <div class="col-6 q-pa-md">
                    <q-input rounded outlined v-model="firstName" label="*Nombre" />
                  </div>
                  <div class="col-6 q-pa-md">
                    <q-input rounded outlined v-model="lastName" label="*Apellido" />
                  </div>
                  <div class="col-6 q-pa-md">
                    <q-input rounded outlined v-model="nameFile" label="*Nombre del archivo" />
                  </div>
                  <div class="col-6 q-pa-md">
                    <q-input rounded outlined v-model="amountPages" type="number" label="*Cantidad de páginas" />
                  </div>
                  <div class="col-6 q-pa-md">
                    <q-file filled bottom-slots v-model="fileDoc" label="*Adjunto" counter>
                      <template v-slot:prepend>
                        <q-icon name="cloud_upload" @click.stop.prevent />
                      </template>
                      <template v-slot:append>
                        <q-icon name="close" @click.stop.prevent="fileDoc = null" class="cursor-pointer" />
                      </template>
                    </q-file>
                  </div>
                  <div class="col-6 q-pa-md">
                    <q-btn color="secondary" v-if="!validEdit" @click="agregarDatos" label="Guardar" />
                    <q-btn color="amber" v-if="validEdit" @click="edit" label="Editar" />
                    <q-btn color="black" v-if="validEdit" @click="cancel" label="Cancelar" />
                  </div>
                </div>
              </q-card-section>
            </q-card>
          </q-expansion-item>
        </div>
      </div>
    </div>
  <div class="container row">
    <div class="col-12 text-right q-pa-md row justify-end">
      <div class="q-mx-md">
        <span class="material-icons q-mx-md md-36 icon-list" @click="viewSelect('list')">
            view_list
          </span>
      </div>
      <div class="q-mx-md">
        <span class="material-icons q-mx-md md-36 icon-list" @click="viewSelect('chart')">
            border_all
          </span>
      </div>
    </div>
    <div class="col-12" v-if="viewFiles === 'chart'">
      <FilesView :dataFiles="dataFiles" @edit="SelectEdit" @delete="deleteFile"
                 @anchor="editAnchor"/>
    </div>
    <div class="col-12" v-if="viewFiles === 'list'">
      <ListFilesView :dataFiles="dataFiles" @edit="SelectEdit" @delete="deleteFile"
                     @anchor="editAnchor"/>
    </div>


  </div>
</template>

<script>
import {ref} from "vue";
import FilesView from "components/FilesView.vue";
import {useQuasar} from "quasar";
import ListFilesView from "components/listFilesView.vue";
export default {
  components: {ListFilesView, FilesView},
  setup(){
    const $q = useQuasar()
    const imageUrl = ref('');
    const validEdit = ref(false)
    const messangeValid = ref('valide los datos Obligatorios(*)')
    const indexSelect = ref(null)
    const extensForm = ref(false)
    const viewFiles = ref("chart")
    const startingInitials = [1, 2, 3, 4, 5, 6, 7, 8, 9];
    const dataFiles = ref([{
      identificationDocumentNumber: "123456",
      firstName: "jonathan",
      lastName: "campos",
      nameFile: "archivo 1",
      amountPages: 5,
      urlFile: '',
      number: 1234567,
      file: null,
      anchor: false
    }])
     const idDocumentNumber = ref(null)
      const firstName = ref('')
      const lastName = ref('')
      const nameFile = ref('')
      const amountPages = ref()
      const number = ref(0)
      const fileDoc = ref(null)
      const anchorSelect = ref(false)

    const randomNumber = () =>{
      const res = [];
      while (res.length < 10) {
        const randomindex = Math.floor(Math.random() * startingInitials.length);
        const randomvalue = startingInitials.splice(randomindex, 1)[0];
        res.push(randomvalue);
      }
      const filteredResult = res.filter((value) => value !== undefined);
      const concatResult = filteredResult.join('');
      return concatResult
    }
    const agregarDatos = () => {

      if (idDocumentNumber.value == null || firstName.value === ""
      || lastName.value === "" || nameFile.value=== "" || amountPages.value==="" ||
      fileDoc.value === null){
        $q.notify({
          type: 'warning',
          message: messangeValid
        })
       return null;
      }
      number.value = Number(randomNumber())
      const payload = {
        identificationDocumentNumber: idDocumentNumber.value,
        firstName: firstName.value,
        lastName: lastName.value,
        nameFile: nameFile.value,
        amountPages: amountPages.value,
        number: number.value,
        urlFile: URL.createObjectURL(fileDoc.value),
        file: fileDoc.value,
        anchor: false
      }
      dataFiles.value.push(payload)
      idDocumentNumber.value = null
      firstName.value = ""
      lastName.value = ""
      nameFile.value = ""
      amountPages.value = ""
      number.value = 0
      fileDoc.value = null
      $q.notify({
        type: 'positive',
        message: 'Agregado correctamente.'
      })
    };
    return {
      idDocumentNumber,
      firstName,
      lastName,
      nameFile,
      amountPages,
      extensForm,
      number,
      fileDoc,
      dataFiles,
      imageUrl,
      validEdit,
      viewFiles,
      indexSelect,
      anchorSelect,
      agregarDatos,
    }
  },
  methods:{
    filterNumberIdentification(identify){
      const filter = this.dataFiles.find(res => res.identificationDocumentNumber === identify);
      if (filter){
        console.log(filter)
        this.idDocumentNumber = filter.identificationDocumentNumber
        this.firstName = filter.firstName
        this.lastName = filter.lastName
      }
    },
    SelectEdit(data){
      this.extensForm = true
      this.validEdit = true
      this.indexSelect = this.dataFiles.findIndex(element => element.identificationDocumentNumber === data.identificationDocumentNumber);
      this.idDocumentNumber = data.identificationDocumentNumber
      this.firstName = data.firstName
      this.lastName = data.lastName
      this.nameFile = data.nameFile
      this.amountPages = data.amountPages
      this.number = data.number
      this.anchorSelect = data.anchor
      this.fileDoc = data.file
    },
    edit(){
      const payload = {
        identificationDocumentNumber: this.idDocumentNumber,
        firstName: this.firstName,
        lastName: this.lastName,
        nameFile: this.nameFile,
        amountPages: this.amountPages,
        number: this.number,
        urlFile: URL.createObjectURL(this.fileDoc),
        anchor: this.anchorSelect,
        file: this.fileDoc
      }
      this.dataFiles[this.indexSelect] = payload
      this.cancel();
    },
    editAnchor(data,anchor){
      const index = this.dataFiles.findIndex(element => element.identificationDocumentNumber === data.identificationDocumentNumber);
      this.dataFiles[index].anchor = anchor

    },
    cancel(){
      this.validEdit = false
      this.idDocumentNumber = null
      this.firstName = ""
      this.lastName = ""
      this.nameFile = ""
      this.amountPages = ""
      this.fileDoc = null
    },
    deleteFile(data){
      const index = this.dataFiles.findIndex(element => element.identificationDocumentNumber === data.identificationDocumentNumber);
      this.dataFiles.splice(index,1)
    },
    viewSelect(res){
      this.viewFiles = res
    }

  }
}
</script>
