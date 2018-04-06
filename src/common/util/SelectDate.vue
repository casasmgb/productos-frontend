<template>
  <div class="select-date">
    <label v-if="label.length">{{ label }}</label>
    <v-layout row wrap>
      <v-flex xs4>
        <v-text-field
        label="Día"
        v-model="form.day"
        maxlength="2"
        @keydown.native="$filter.numeric($event)"
        :rules="required ? $validate(['required']) : []"
        :required="required"
        ></v-text-field>
      </v-flex>
      <v-flex xs4>
        <v-text-field
        label="Mes"
        v-model="form.month"
        maxlength="2"
        @keydown.native="$filter.numeric($event)"
        :rules="required ? $validate(['required']) : []"
        :required="required"
        ></v-text-field>
      </v-flex>
      <v-flex xs4>
        <v-text-field
        label="Año"
        v-model="form.year"
        maxlength="4"
        @keydown.native="$filter.numeric($event)"
        :rules="required ? $validate(['required']) : []"
        :required="required"
        ></v-text-field>
      </v-flex>
    </v-layout>
  </div>
</template>

<script>
import validate from '@/common/mixins/validate';

export default {
  mixins: [ validate ],
  props: {
    label: {
      type: String,
      default: ''
    },
    required: {
      type: Boolean,
      default: false
    },
    model: {
      type: String,
      default: ''
    },
    store: {
      type: String,
      default: ''
    }
  },
  data () {
    return {
      form: {
        day: '',
        month: '',
        year: ''
      },
      meses: this.$datetime.months
    };
  },
  methods: {
    setDate () {
      if (this.form.year && this.form.year.length === 4 && this.form.month && this.form.day) {
        let date = new Date(this.form.year, this.form.month - 1, this.form.day);
        this.$store.commit('setDate', this.model.length ? { [this.model]: date } : date);
        if (this.model.length) {
          this.$store.commit(`${this.store}updateField`, { path: this.model, value: date });
        }
      } else {
        this.$store.commit('setDate', this.model.length ? { [this.model]: null } : null);
        if (this.model.length) {
          this.$store.commit(`${this.store}updateField`, { path: this.model, value: null });
        }
      }
    }
  },
  watch: {
    'form.day': function (val) {
      if (val) {
        if (val > 31 || (val.length === 2 && val <= 0)) {
          this.form.day = '';
        }
        this.setDate();
      }
    },
    'form.month': function (val) {
      if (val) {
        if (val > 12 || (val.length === 2 && val <= 0)) {
          this.form.month = '';
        }
        this.setDate();
      }
    },
    'form.year': function (val) {
      if (val) {
        if (val.length === 4 && (val > this.$datetime.now('YYYY') || val < 1900)) {
          this.form.year = '';
        }
        this.setDate();
      }
    },
    '$store.state.touch': function (val) {
      if (!val) {
      }
      this.$store.commit('setTouch', false);
    }
  }
};
</script>

<style lang="scss">
  .select-date {
    label {
      color: rgba(0,0,0,0.54);
    }
  }
</style>
