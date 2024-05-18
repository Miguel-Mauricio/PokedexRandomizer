<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';


const pokemonImageSrc = ref('');
const pokemonName = ref('');
const pokemonHp = ref('');
const pokemonType = ref('');
const pokemonAbilities = ref('');

const getPokemon = async () => {
    try {
        const response = await axios.get('https://pokeapi.co/api/v2/pokemon?limit=1025');
        const data = response.data;
        const randomIndex = Math.floor(Math.random() * 1025);
        const randomPokemon = data.results[randomIndex];
        return randomPokemon.name;
    } catch (error) {
        console.error("Error fetching Pokémon:", error);
    }
};

const fetchNewPokemon = async () => {
    const randomPokemonName = await getPokemon();
    if (randomPokemonName) {
        displayPokemon(randomPokemonName);
    }
};
const displayPokemon = async (name) => {
    try {
        const response = await axios.get(`https://pokeapi.co/api/v2/pokemon/${name}`);
        const data = response.data;
        pokemonImageSrc.value = data.sprites.front_default;
        pokemonName.value = data.name;
        pokemonHp.value = data.stats[0].base_stat;
        pokemonType.value = data.types.map(type => type.type.name).join(', ');

        const abilitiesResponse = await axios.get(`https://pokeapi.co/api/v2/ability?limit=100`);
        const abilitiesData = abilitiesResponse.data;

        const pokemonAbilitiesArray = data.abilities.map(ability => abilitiesData.results.find(a => a.name === ability.ability.name)).filter(Boolean);
        pokemonAbilities.value = pokemonAbilitiesArray.map(ability => ability.name).join(', ');

    } catch (error) {
        console.error("Error fetching Pokémon details:", error);
    }
};

onMounted(async () => {
    const randomPokemon = await getPokemon();
    if (randomPokemon) {
        displayPokemon(randomPokemon);
    }
});

</script>





<template>

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pokémon Randomizer</title>
</head>
<body>
    <div class="pokemon-container">
        <img :src="pokemonImageSrc" alt="Random Pokémon">
        <h2>{{ pokemonName }}</h2>
        <p>HP: {{ pokemonHp }}</p>
        <p>Type: {{ pokemonType }}</p>
        <p>Abilities: {{ pokemonAbilities }}</p>
    </div>
    <button id="randomizeButton" @click="fetchNewPokemon">Get Random Pokemon</button>
    
</body>

</template>

<style scoped>
body {
    font-family: Arial, sans-serif;
    text-align: center;
    background-image: url('https://cdnb.artstation.com/p/assets/images/images/021/500/833/large/mario-aceituno-carcasa-pokedex-completa.jpg?1571920182');
    background-size: cover; /* Change from 'auto' to 'cover' */
    background-position: center; 
    display: flex; /* Add this line to enable flexbox layout */
    justify-content: center; /* Center children horizontally */
    align-items: center; /* Center children vertically */
    min-height: 100vh; /* Ensure body takes at least the full viewport height */
    margin: 0; /* Remove default margins */
}

.pokemon-container {
    margin-top: 50px;
}

#pokemon-image {
    width: 200px;
    height: auto;
}

#randomizeButton {
    padding: 10px 20px;
    background-color: #4CAF50;
    color: white;
    border: none;
    cursor: pointer;
}
</style>