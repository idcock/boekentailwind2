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
        <button
          @click="nieuwBoek"
          class="border-2 rounded p-2 hover:bg-blue-600"
        >
          Nieuw boek
        </button>
      </template>
      <template v-slot:acties="{ item }">
        <td>
          <i @click="wijzigBoek(item)" class="material-icons cursor-pointer">
            edit
          </i>
          <i @click="verwijderBoek(item)" class="material-icons cursor-pointer">
            delete
          </i>
        </td>
      </template>
    </data-tabel>
    <modal @sluiten="toonModal = false" v-show="toonModal">
      <h1 class="text-2xl text-center mb-6">
        Boek {{ wijzigen ? "wijzigen" : "toevoegen" }}
      </h1>
      <div class="flex">
        <label class="w-48" for="titel">Titel:</label>
        <input
          class="flex-grow border-b border-gray-200 mb-6 outline-none"
          type="text"
          id="titel"
          v-model="boek.titel"
        />
      </div>
      <div class="flex">
        <label class="w-48" for="aantalPaginas">Aantal pagina's:</label>
        <input
          class="flex-grow border-b border-gray-200 mb-6 outline-none"
          type="text"
          id="aantalPaginas"
          v-model.number="boek.aantalPaginas"
        />
      </div>
      <div class="flex">
        <label class="w-48" for="auteur">Auteur:</label>
        <select class="flex-grow border-b border-gray-200 mb-6 outline-none">
          <option
            v-for="auteur of auteurs"
            :key="auteur._id"
            :value="auteur._id"
          >
            {{ auteur.naam }}
          </option></select
        >
      </div>
      <div class="flex justify-around">
        <button
          class="rounded border border-1 border-gray-400 px-4 py-2
        text-black  w-32"
          @click="toonModal = false"
        >
          Annuleren
        </button>

        <button
          @click="postData"
          class="rounded bg-blue-400 px-4 py-2
        text-white  w-32"
        >
          {{ wijzigen ? "Wijzigen" : "Toevoegen" }}
        </button>
      </div>
    </modal>
  </div>
</template>

<script>
import axios from "axios";
import DataTabel from "@/components/DataTabel.vue";
import Modal from "@/components/Modal.vue";

export default {
  name: "home",
  components: {
    DataTabel,
    Modal
  },
  data() {
    return {
      boek: {
        titel: "",
        aantalPaginas: 1,
        auteur: ""
      },
      auteurs: [],
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
      ],
      toonModal: false,
      wijzigen: false
    };
  },
  methods: {
    nieuwBoek() {
      this.wijzigen = false;
      this.boek = {
        titel: "",
        aantalPaginas: 1,
        auteur: ""
      };
      this.toonModal = true;
    },
    wijzigBoek(boek) {
      this.wijzigen = true;
      this.boek = {
        ...boek,
        auteur: boek.auteur._id
      };
      this.toonModal = true;
    },
    async verwijderBoek(boek) {
      // boek verwijderen
      //{item} in slot is een deconstructie van het object dat doorgegeven wordt vanuit kind
      try {
        await axios.delete("http://localhost:7000/boeken/" + boek._id);
        //manuele aanpassing van array kan hier omdat we op deze plaats zeker zijn dat het gelukt is
        this.boeken.splice(this.boeken.indexOf(boek), 1);
      } catch (err) {
        console.log(err);
      }
    },
    async postData() {
      if (this.wijzigen) {
        //wijzigen boek databank
        const gewijzigdBoek = await axios.put(
          "http://localhost:7000/boeken/" + this.boek._id,
          this.boek
        );

        const index = this.boeken.findIndex(boek => {
          return boek._id === this.boek._id;
        });
        this.boeken.splice(index, 1, gewijzigdBoek.data);
        this.toonModal = false;
      } else {
        // toevoegen van boek in db
        const nieuwBoek = await axios.post(
          "http://localhost:7000/boeken",
          this.boek
        );
        this.boeken.push(nieuwBoek.data);
        this.toonModal = false;
      }
    }
  },
  async created() {
    // Data ophalen van server
    const boeken = await axios("http://localhost:7000/boeken");
    this.boeken = boeken.data;

    const auteurs = await axios("http://localhost:7000/auteurs");
    this.auteurs = auteurs.data;
  }
};
</script>
