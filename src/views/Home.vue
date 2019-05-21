<template>
  <div>
    <img
      class="w-1/5 mx-auto mb-8"
      src="@/assets/images/boeken.jpg"
      alt="Afbeelding boeken"
    />
    <data-tabel
      titel="Boekbeheer"
      :items="boeken"
      :kolommen="kolommen"
      itemKey="_id"
    >
      <template v-slot:titel-knop>
        <button class="border-2 rounded p-2 hover:bg-blue-600">
          Nieuw boek
        </button>
      </template>
    </data-tabel>
  </div>
</template>

<script>
import axios from "axios";
import DataTabel from "@/components/DataTabel.vue";

export default {
  name: "home",
  components: {
    DataTabel
  },
  data() {
    return {
      boeken: [],
      kolommen: [
        {
          naam: "Titel",
          veld: "titel",
          sorteerbaar: true,
          zoekbaar: true
        },
        {
          naam: "Auteur",
          veld: "auteur.naam",
          sorteerbaar: true,
          zoekbaar: true
        },
        {
          naam: "Aantal Pagina's",
          veld: "aantalPaginas",
          sorteerbaar: true,
          zoekbaar: false
        }
      ]
    };
  },
  async created() {
    // Data ophalen van server
    const boeken = await axios("http://localhost:7000/boeken");
    this.boeken = boeken.data;
  }
};
</script>
