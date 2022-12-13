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
        <q-btn
          label="Delete"
          type="delete"
          color="primary"
          @click="deleteLaptope(laptop.id)"
        />
        <q-btn label="Edit" color="primary" @click="edit = true" />
      </q-card>
    </div>
    <div class="q-pa-md q-gutter-sm">
      <q-dialog v-model="edit" id="edit" persistent>
        <q-card style="min-width: 350px">
          <q-card-section>
            <div class="text-h6">Edit</div>
          </q-card-section>

          <q-card-section class="q-pt-none">
            <q-input
              filled
              type="double"
              v-model="state.formdata.marka"
              label="Marka"
              lazy-rules
              :rules="[
                (val) => (val !== null && val !== '') || 'Unesite marku',
              ]"
            />
            <q-input
              filled
              type="double"
              v-model="state.formdata.model"
              label="Model"
              lazy-rules
              :rules="[
                (val) => (val !== null && val !== '') || 'Unesite model',
              ]"
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
          </q-card-section>

          <q-card-actions align="right" class="text-primary">
            <q-btn flat label="Zatvori" v-close-popup />
            <q-btn flat label="Spremi" v-close-popup />
          </q-card-actions>
        </q-card>
      </q-dialog>
    </div>
  </div>
</template>

<script>
import { reactive, onMounted } from "vue";
import {
  addDoc,
  collection,
  query,
  onSnapshot,
  deleteDoc,
  doc,
} from "@firebase/firestore";
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

    let unsubscribe;

    const dohvatiLaptope = async () => {
      const q = query(collection(db, "laptop"));
      /* const querySnapshot = await getDocs(collection(db, "laptop")); */
      unsubscribe = onSnapshot(q, (querySnapshot) => {
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
      });
    };

    const deleteLaptope = async (id) => {
      await deleteDoc(doc(db, "laptop", id));
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
      deleteLaptope,
      edit:true,
    };
  },
};
</script>
