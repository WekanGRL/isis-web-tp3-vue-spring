<template>
  <main>
    <div>
      <h1>Les catégories de produits</h1>
      <select @change="chargeProduits()" v-model="categorieSelectionnee">
        <option selected="true" disabled="disabled">Sélectionnez une catégorie</option>
        <option v-for="categorie in data.listeCategories" :key="categorie.code" :value="categorie._links.produits.href">
          {{ categorie.libelle }}
        </option>
      </select>
    </div>
    <div>
      <table>
        <caption>Les produits</caption>
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
      </table>
    </div>
  </main>
</template>

<script setup>
import {reactive, onMounted, ref} from "vue";
import { doAjaxRequest } from "@/api";

const categorieSelectionnee = ref("");

let data = reactive({
  // La liste des catégories affichée sous forme de table
  listeCategories : [],
  listeProduits: [],
});

function showError(error) {
  console.log("Erreur : status %d", error.status)
  console.log(error.body);
  alert(error.message);
}

function chargeCategories() {
  // Appel à l'API pour avoir la liste des catégories
  // Trié par code, descendant
  // Verbe HTTP GET par défaut
  doAjaxRequest("/api/categories?sort=code,desc")
      .then((json) => {
        data.listeCategories = json._embedded.categories;
      })
      .catch(showError);
}

function chargeProduits() {
  doAjaxRequest(categorieSelectionnee._value)
      .then((json) => {
        data.listeProduits = json._embedded.produits;
      })
      .catch(showError);
}

// A l'affichage du composant, on charge la liste des catégories
onMounted(chargeCategories);
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
