<template>
    <main>
        <div>Les clients - page {{data.listePage.number+1}}/{{data.listePage.totalPages}}</div>
        <div>
            <table>
                <tr>
                    <th>Code</th>
                    <th>Société</th>
                    <th>Contact</th>
                    <th>Ville</th>
                </tr>
                <!-- Si le tableau des catégories est vide -->
                <tr v-if="data.listeClient.length === 0">
                    <td colspan="4">Veuillez patienter, chargement des clients...</td>
                </tr>
                <!-- Si le tableau des catégories n'est pas vide -->
                <tr v-for="client in data.listeClient" :key="client.code">
                    <td>{{ client.code }}</td>
                    <td>{{ client.societe }}</td>
                    <td>{{ client.contact }}</td>
                    <td>{{ client.adresse.ville }}</td>
                </tr>
                <tr>
                    <td><button @click="chargeClients()">first page</button></td>
                    <td><button @click="NextPage(data.listePage.number-1)">page d'avant</button></td>
                    <td><button @click="NextPage(data.listePage.number+1)">page d'après</button></td>
                    <td><button @click="NextPage(data.listePage.totalPages-1)">dernière page</button></td>
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
    listeClient: [],
    listePage: []
});


function showError(error) {
    console.log("Erreur : status %d", error.status)
    console.log(error.body);
    alert(error.message);
}

function chargeClients() {
    doAjaxRequest("/api/clients?page=0&size=5")
        .then((json) => {
            data.listeClient = json._embedded.clients;
            data.listePage = json.page;
        })
        .catch(showError);
}

function NextPage(index) {
    doAjaxRequest("/api/clients?page="+index+"&size=5")
        .then((json) => {
            data.listeClient = json._embedded.clients;
            data.listePage = json.page;
        })
        .catch(showError);
}



// A l'affichage du composant, on affiche la liste
onMounted(chargeClients);

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
