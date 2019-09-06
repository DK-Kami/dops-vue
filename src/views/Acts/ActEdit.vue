<template>
  <v-layout justify-center>
    <v-flex xs5>
      <v-form v-model="valid" @submit.prevent="action">
        <v-card class="pa-4">
          <v-card-title class="mb-5">{{ currentTitle }}</v-card-title>

          <v-card-text>
            <v-text-field
              v-model.trim="act.fio"
              :rules="[rules.required, rules.fio]"
              label="ФИО"
              outlined
              rounded
              shaped
            />
            
            <v-text-field
              v-model.trim="act.phone"
              :rules="[rules.required, rules.phone]"
              v-mask="'(###) ###-##-##'"
              label="Телефон"
              prefix="+7 "
              outlined
              rounded
              shaped
            />
            
            <v-text-field
              v-model.trim="act.inn"
              :rules="[rules.required, rules.inn]"
              v-mask="'##### #####'"
              label="ИНН"
              outlined
              rounded
              shaped
            />
            
            <v-layout>
              <v-text-field
                v-model.trim="act.serialPassport"
                :rules="[rules.required, rules.seria]"
                label="Серия паспорта"
                v-mask="'####'"
                class="mr-4"
                outlined
                rounded
                shaped
              />
              
              <v-text-field
                v-model.trim="act.numberPassport"
                :rules="[rules.required, rules.numberPassport]"
                label="Номер паспорта"
                v-mask="'######'"
                outlined
                rounded
                shaped
              />
            </v-layout>
          </v-card-text>

          <v-card-actions>
            <v-spacer />
            <v-btn
              :disabled="!valid"
              :loading="loading"
              color="primary"
              type="submit"
              depressed
              outlined
              rounded
              large
            >{{ currentActionName }}</v-btn>
          </v-card-actions>
        </v-card>
      </v-form>
    </v-flex>
  </v-layout>
</template>

<script>
import { mask } from 'vue-the-mask';

export default {
  name: 'ActEdit',
  directives: { mask },
  props: {
    id: { act: Number },
  },
  async created() {
    if (this.isEdit) {
      this.act = (await this.$store.dispatch('acts/loadAct', this.id)).data;
    }
  },
  data: vm => ({
    act: {
      serialPassport: '',
      numberPassport: '',
      phone: '',
      inn: '',
      fio: '',
    },
    valid: false,
    loading: false,
    rules: vm.$store.getters.getRules,
  }),
  computed: {
    isEdit() {
      return !!this.id;
    },
    currentTitle() {
      return this.isEdit ? 'Редактирование типа' : 'Создание нового типа'
    },
    currentActionName() {
      return this.isEdit ? 'Редактировать' : 'Создать';
    }
  },
  methods: {
    action() {
      if (this.isEdit) this.edit();
      else this.create();
    },
    async create() {
      this.loading = true;
      await this.$store.dispatch('acts/addAct', this.act);
      this.loading = false;

      setTimeout(() => {
        this.$router.push('/acts');
      }, 1000);
    },
    async edit() {
      this.loading = true;
      await this.$store.dispatch('acts/editAct', {
        data: this.act,
        id: this.id,
      });
      this.loading = false;

      setTimeout(() => {
        this.$router.push('/acts');
      }, 1000);
    }
  }
};
</script>