<template>
  <div class="q-pa-md fit row q-col-gutter-md">
    <div class="col-6" style="max-width: 400px">
      <q-form @submit.prevent="submitform" class="q-gutter-md">
        <q-input
          filled
          type="double"
          v-model="state.formdata.marka"
          label="Marka"
          lazy-rules
          :rules="[(val) => (val !== null && val !== '') || 'Unesite marku']"
        />
        <q-input
          filled
          type="double"
          v-model="state.formdata.model"
          label="Model"
          lazy-rules
          :rules="[(val) => (val !== null && val !== '') || 'Unesite model']"
        />

        <q-input
          filled
          type="double"
          v-model="state.formdata.komponente"
          label="Komponente"
          lazy-rules
          :rules="[
            (val) => (val !== null && val !== '') || 'Unesite komponente',
          ]"
        />
        <div>{{ state.poruka }}</div>
        <div>
          <q-btn label="Submit" type="submit" color="primary" />
          <q-btn
            label="Reset"
            type="reset"
            color="primary"
            flat
            class="q-ml-sm"
            @click="onReset"
          />
        </div>
      </q-form>
    </div>
    <div class="offset-2" v-if="state.listaLaptopa.length > 0">
      <q-card
        class="my-card q-pa-md"
        v-for="laptop in state.listaLaptopa"
        :key="laptop.id"
      >
        <q-card-section>
          Marka: {{ laptop.marka }}
          <q-separator color="grey" spaced horizontal />
        </q-card-section>
        <q-card-section>
          Model: {{ laptop.model }} <q-separator color="grey" spaced horizontal
        /></q-card-section>
        <q-card-section>
          Komponente: {{ laptop.komponente }}
          <q-separator color="grey" spaced horizontal
        /></q-card-section>
      </q-card>
    </div>
  </div>
</template>

<script>
import { reactive, onMounted } from "vue";
import { addDoc, collection, getDocs } from "@firebase/firestore";
import { db } from "src/boot/firebase";

export default {
  name: "laptopInfo",
  setup() {
    const state = reactive({
      formdata: {
        marka: "",
        model: "",
        komponente: "",
      },
      poruka: "",
      listaLaptopa: [],
    });

    async function submitform() {
      await addDoc(collection(db, "laptop"), {
        marka: state.formdata.marka,
        model: state.formdata.model,
        komponente: state.formdata.komponente,
      });
      state.formdata.marka = " ";
      state.formdata.model = " ";
      state.formdata.komponente = " ";
    }

    const dohvatiLaptope = async () => {
      const querySnapshot = await getDocs(collection(db, "laptop"));
      state.listaLaptopa = [];
      querySnapshot.forEach((doc) => {
        let laptop = {
          id: doc.id,
          marka: doc.data().marka,
          model: doc.data().model,
          komponente: doc.data().komponente,
        };

        state.listaLaptopa.push(laptop);
      });
    };
    onMounted(() => {
      dohvatiLaptope();
    });

    const onReset = () => {
      state.formdata.marka = " ";
      state.formdata.model = " ";
      state.formdata.komponente = " ";
    };

    return {
      submitform,
      onReset,
      state,
    };
  },
};
</script>
