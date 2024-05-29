<template>
  <main>
    <div>
      <table>
        <caption>Les produits - Page {{ data.infosPage.number + 1 }}/{{ data.infosPage.totalPages }}</caption>
        <tr>
          <th>Nom</th>
          <th>Prix</th>
          <th>Stock</th>
          <th>Commandés</th>
        </tr>
        <!-- Si le tableau des catégories est vide -->
        <tr v-if="data.listeProduits.length === 0">
          <td colspan="4">Veuillez patienter, chargement des produits...</td>
        </tr>
        <!-- Si le tableau des catégories n'est pas vide -->
        <tr v-for="produit in data.listeProduits" :key="produit.reference">
          <td>{{ produit.nom }}</td>
          <td>{{ produit.prixUnitaire }}</td>
          <td>{{ produit.unitesEnStock }}</td>
          <td>{{ produit.unitesCommandees }}</td>
        </tr>
        <tr>
          <td>
            <button :disabled="data.infosPage.number === 0" @click="chargeProduits(data.liens.first.href)">
              ⇇
            </button>
          </td>
          <td>
            <button :disabled="data.infosPage.number === 0" @click="chargeProduits(data.liens.prev.href)">
              ←
            </button>
          </td>
          <td>
            <button :disabled="data.infosPage.number + 1 === data.infosPage.totalPages" @click="chargeProduits(data.liens.next.href)">
              →
            </button>
          </td>
          <td>
            <button :disabled="data.infosPage.number + 1 === data.infosPage.totalPages" @click="chargeProduits(data.liens.last.href)">
              ⇉
            </button>
          </td>
        </tr>
      </table>
    </div>
  </main>
</template>

<script setup>
import { reactive, onMounted } from "vue";
import { doAjaxRequest } from "@/api";

let data = reactive({
  // La liste des catégories affichée sous forme de table
  listeProduits: [],
  liens: [],
  infosPage: [],
});

function showError(error) {
  console.log("Erreur : status %d", error.status)
  console.log(error.body);
  alert(error.message);
}

function firstChargeProduits() {
  chargeProduits("/api/produits?size=5&page=0&sort=nom,asc");
}

function chargeProduits(linkRef) {
  doAjaxRequest(linkRef)
      .then((json) => {
        data.listeProduits = json._embedded.produits;
        data.liens = json._links;
        data.infosPage = json.page;
      })
      .catch(showError);
}

// A l'affichage du composant, on affiche la liste
onMounted(firstChargeProduits);
</script>


<style scoped>
td,
th {
  border: 1px solid #ddd;
  padding: 8px;
}

th {
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: left;
  background-color: #232623;
  color: rgb(255, 255, 255);
}
</style>
