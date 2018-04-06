<template>
  <div class="file-upload">
    <vue-dropzone
      ref="myVueDropzone"
      id="dropzone"
      @vdropzone-success="vdropzoneSuccess"
      @vdropzone-error="vdropzoneError"
      :options="dropzoneOptions">
    </vue-dropzone>
  </div>
</template>
<script>
import vue2Dropzone from 'vue2-dropzone';
import 'vue2-dropzone/dist/vue2Dropzone.css';

export default {
  props: {
    url: {
      type: String,
      default: 'http://localhost/upload'
    },
    label: {
      type: String,
      default: 'Arrastra tus archivos aquí'
    },
    name: {
      type: String,
      default: 'file'
    },
    types: {
      type: String,
      default: 'application/pdf'
    }
  },
  data () {
    return {
      dropzoneOptions: {
        url: this.url,
        maxFilesize: 5,
        paramName: this.name,
        acceptedFiles: this.types,
        maxFiles: 1,
        addRemoveLinks: true,
        dictDefaultMessage: `<i aria-hidden="true" class="material-icons icon primary--text">cloud_upload</i> ${this.label}`,
        dictFallbackMessage: 'Tu navegador no soporta drag and drop.',
        dictFallbackText: 'Usa el formulario de abajo para subir tus archivos como en los viejos tiempos.',
        dictFileTooBig: 'Archivo muy grande ({{filesize}}MiB). Máximo permitido: {{maxFilesize}}MiB.',
        dictInvalidFileType: 'No puede subir este tipo de archivo',
        dictResponseError: 'El servidor respondió con código {{statusCode}}.',
        dictCancelUpload: 'Cancelar subida.',
        dictCancelUploadConfirmation: 'Esta seguro que quiere cancelar?',
        dictRemoveFile: 'Eliminar archivo',
        dictMaxFilesExceeded: 'No puede subir mas archivos.',
        headers: {
          Authorization: `${this.$authToken} ${this.$storage.get('token')}`
        }
      }
    };
  },
  components: {
    vueDropzone: vue2Dropzone
  },
  methods: {
    vdropzoneSuccess (file, response) {
      console.log('Archivo subido', file, response);
      if (response && response.data && response.data.id_archivo) {
        this.$store.commit('setArchivo', response.data.id_archivo);
      }
    },
    vdropzoneError (file, message, xhr) {
      this.$message.error(message);
    }
  }
};
</script>

<style lang="scss">
.file-upload {
  margin-bottom: 20px;
}
</style>
