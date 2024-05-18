<script setup>

import { ref, onMounted } from 'vue';
import axios from 'axios';

alert("To change pokemnons press the white circle");

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
    <div class="pokemon-description", id="description">
        <img :src="pokemonImageSrc" alt="Random Pokémon">
        <h2>{{ pokemonName }}</h2>
        <p>HP: {{ pokemonHp }}</p>
        <p>Type: {{ pokemonType }}</p>
        <p>Abilities: {{ pokemonAbilities }}</p>
    </div>
    <button id="randomizeButton" @click="fetchNewPokemon"></button>
    
</body>

</template>

<style scoped>
body {
    font-family: 'Press Start 2P', Arial, sans-serif;
    text-align: center;
    background-image: url('https://cdnb.artstation.com/p/assets/images/images/021/500/833/large/mario-aceituno-carcasa-pokedex-completa.jpg?1571920182');
    background-position: center;
    display: flex; 
    justify-content: center; 
    align-items: center; 
    min-height: 98vh; 
    margin: 0; 
    padding: 0%;
}

#description {
    align-items: center;
    width: 100%;
    height: auto; 
    max-width: 600px; 
    padding: 20px; 
    color: #800101;
    margin: auto;
    background-image: url('https://img.freepik.com/free-vector/gradient-zoom-effect-blue-background_23-2149762303.jpg');
}



@media (max-width: 768px) {
   .pokemon-container {
        width: 90%; 
    }
}

#randomizeButton {
    padding: 50px 50px; 
    left:100%;
    top: 50%;
    cursor: pointer;
    border-radius: 50%; 
    transition: transform 0.3s ease-in-out; 
    
}

#randomizeButton:hover {
    transform: scale(1.1); 
    background-image: url('https://icon-library.com/images/small-pokeball-icon/small-pokeball-icon-4.jpg'); 
    background-repeat: no-repeat;
    background-size: contain;
    background-position: center;
}
</style>

