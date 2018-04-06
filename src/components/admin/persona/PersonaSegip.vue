<template>
  <div>
    <v-layout row wrap>
      <v-flex xs4>
        <v-select
          :items="tiposDoc"
          v-model="tipo_documento"
          label="Tipo de documento"
          item-text="text"
          item-value="value"
          :rules="$validate(['required'])"
          required
          ></v-select>
      </v-flex>
      <v-flex xs3>
        <v-text-field
          label="Número de documento"
          v-model="nro_documento"
          maxlength="20"
          @keydown.native="$filter.numeric($event)"
          :rules="$validate(['required'])"
          required
          ></v-text-field>
      </v-flex>
      <v-flex xs3>
        <select-date
          model="form.fecha_nacimiento"
          store="usuario/"
          :required="true"
        >
        </select-date>
      </v-flex>
      <v-flex xs2>
        <v-btn
          color="info"
          :disabled="$filter.empty(tipo_documento) || $filter.empty(nro_documento) || $filter.empty(fecha_nacimiento)"
          @click="buscarPersona"><v-icon>search</v-icon> Buscar</v-btn>
      </v-flex>
    </v-layout>
  </div>
</template>

<script>
import SelectDate from '@/common/util/SelectDate';
import validate from '@/common/mixins/validate';
import { mapFields } from 'vuex-map-fields';

export default {
  mixins: [ validate ],
  props: {
    store: {
      type: String,
      default: ''
    }
  },
  data () {
    return {
      tiposDoc: [
        { value: 'CI', text: 'CÉDULA DE IDENTIDAD' },
        { value: 'PASAPORTE', text: 'PASAPORTE' }
      ]
    };
  },
  beforeCreate () {
    // Creando campos del formulario en el store definido en props
    let store = this.$options.propsData.store || '';
    this.$options.computed = {
      ...mapFields([
        'form.tipo_documento',
        'form.tipo_documentoOtro',
        'form.nro_documento',
        'form.fecha_nacimiento'
      ], `${store}getField`, `${store}updateField`)
    };
  },
  methods: {
    buscarPersona () {
      this.$service.get(`system/persona-segip/${this.nro_documento}?fechaNacimiento=${this.$datetime.format(this.fecha_nacimiento)}`)
      .then(response => {
        if (response && response.persona) {
          const persona = response.persona;
          this.$store.commit('usuario/setForm', {
            primer_apellido: persona.paterno,
            segundo_apellido: persona.materno,
            nombres: persona.nombres,
            nacionalidad: persona.nacionalidad
          });
        }
      });
    }
  },
  components: {
    SelectDate
  }
};
</script>
