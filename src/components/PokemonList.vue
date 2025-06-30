<template>
  <div class="pokedex-wrapper">
    <!-- Titre + barre de recherche -->
    <div class="flex justify-between items-center mb-6 px-4 flex-wrap gap-4">
      <h1 class="text-4xl font-bold text-orange-600 drop-shadow-lg">POKÉDEX</h1>
      <input
        v-model="searchQuery"
        type="text"
        placeholder="Rechercher un Pokémon..."
        class="border border-orange-300 rounded-lg px-4 py-2 shadow focus:outline-none w-full sm:w-auto"
      />
    </div>

    <!-- Boutons du haut -->
    <div class="flex justify-between items-center mb-4 px-4 flex-wrap gap-4">
      <!-- Bouton Charger -->
      <div class="flex gap-4 items-center">
        <button
          class="bg-orange-500 hover:bg-orange-600 text-white font-bold py-3 px-6 rounded-full shadow-lg transition"
          @click="loadMore"
        >
          Charger plus de Pokémons
        </button>

        <!-- Tri -->
        <div class="flex items-center gap-2">
          <label class="font-semibold text-orange-700">Trier :</label>
          <select v-model="sortOrder" @change="sortPokemons" class="border rounded px-3 py-1 shadow">
            <option value="asc">A → Z</option>
            <option value="desc">Z → A</option>
          </select>
        </div>
      </div>

      <!-- Bouton Pokédex -->
      <button
        class="bg-yellow-400 hover:bg-yellow-500 text-orange-900 font-bold py-2 px-4 rounded-full shadow transition"
        @click="showPokedex = true"
        title="Voir le Pokédex"
      >
        📘 Mon Pokédex ({{ pokedex.length }})
      </button>
    </div>

    <!-- Filtres Types -->
    <div class="flex flex-wrap justify-center gap-2 mb-8 px-4">
      <button
        v-for="type in typeOptions"
        :key="type"
        @click="selectedType = type"
        :class="[
          'px-4 py-2 rounded-lg text-white font-semibold transition',
          selectedType === type ? 'bg-orange-600' : 'bg-orange-200 hover:bg-orange-400'
        ]"
      >
        {{ type === 'all' ? 'Tous les pokémon' : type }}
      </button>
    </div>

    <!-- Cartes Pokémons -->
    <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-6 px-4">
      <div
        v-for="pokemon in filteredPokemons"
        :key="pokemon.name"
        class="pokemon-card relative"
        @click="selectedPokemon = pokemon"
      >
        <h2>{{ pokemon.name }}</h2>
        <img :src="pokemon.image" alt="pokemon" />
        <p class="ability-title">Compétences :</p>
        <ul class="ability-list">
          <li v-for="ability in pokemon.abilities" :key="ability.ability.name">
            {{ ability.ability.name }}
          </li>
        </ul>

        <!-- Bouton + -->
        <button
          class="absolute top-2 right-2 bg-white text-orange-600 rounded-full px-2 py-1 font-bold text-xl shadow hover:bg-orange-100"
          @click.stop="addToPokedex(pokemon)"
        >
          +
        </button>
      </div>
    </div>

    <!-- Modales -->
    <PokemonModal
      v-if="selectedPokemon"
      :pokemon="selectedPokemon"
      @close="selectedPokemon = null"
    />
    <PokedexModal
      v-if="showPokedex"
      :pokedex="pokedex"
      @close="showPokedex = false"
      @remove="removeFromPokedex"
    />
  </div>
</template>

<script>
import PokemonModal from './PokemonModal.vue'
import PokedexModal from './PokedexModal.vue'

export default {
  name: 'PokemonList',
  components: { PokemonModal, PokedexModal },
  data() {
    return {
      pokemons: [],
      pokedex: [],
      limit: 16,
      offset: 0,
      sortOrder: 'asc',
      selectedType: '',
      searchQuery: '',
      selectedPokemon: null,
      showPokedex: false,
      typeOptions: [
        'all', 'normal', 'fire', 'water', 'grass', 'electric',
        'ice', 'fighting', 'poison', 'ground', 'flying',
        'psychic', 'bug', 'rock', 'ghost', 'dragon',
        'dark', 'steel', 'fairy'
      ]
    }
  },
  mounted() {
    this.fetchPokemons()
  },
  computed: {
    filteredPokemons() {
      const filtered = this.selectedType === '' || this.selectedType === 'all'
        ? this.pokemons
        : this.pokemons.filter(p =>
            p.types.some(t => t.type.name === this.selectedType)
          )

      return filtered.filter(p =>
        this.normalize(p.name).includes(this.normalize(this.searchQuery))
      )
    }
  },
  methods: {
    async fetchPokemons() {
      try {
        const response = await fetch(`https://pokeapi.co/api/v2/pokemon?limit=${this.limit}&offset=${this.offset}`)
        const data = await response.json()

        const promises = data.results.map(async (p) => {
          const res = await fetch(p.url)
          const fullData = await res.json()
          return {
            name: fullData.name,
            image: fullData.sprites.front_default,
            abilities: fullData.abilities,
            types: fullData.types
          }
        })

        const newPokemons = await Promise.all(promises)
        this.pokemons.push(...newPokemons)
        this.sortPokemons()
      } catch (error) {
        console.error('Erreur chargement pokémons:', error)
      }
    },
    loadMore() {
      this.offset += this.limit
      this.fetchPokemons()
    },
    sortPokemons() {
      this.pokemons.sort((a, b) => {
        const nameA = a.name.toLowerCase()
        const nameB = b.name.toLowerCase()
        return this.sortOrder === 'asc'
          ? nameA.localeCompare(nameB)
          : nameB.localeCompare(nameA)
      })
    },
    normalize(str) {
      return str.normalize('NFD').replace(/[\u0300-\u036f]/g, '').toLowerCase()
    },
    addToPokedex(pokemon) {
      if (!this.pokedex.find(p => p.name === pokemon.name)) {
        this.pokedex.push(pokemon)
      }
    },
    removeFromPokedex(pokemonName) {
      this.pokedex = this.pokedex.filter(p => p.name !== pokemonName)
    }
  }
}
</script>

<style>
.pokedex-wrapper {
  width: 100%;
  padding: 20px;
  background: linear-gradient(to bottom, #fffbe6, #ffe5b4);
  min-height: 100vh;
  border: 5px dashed rgb(255, 102, 0);
}

.pokemon-card {
  background: linear-gradient(135deg, #fbd85d, #f18f01);
  border: 3px solid #e67205;
  border-radius: 16px;
  padding: 16px;
  text-align: center;
  width: 100%;
  max-width: 240px;
  box-shadow: 4px 4px 12px rgba(0, 0, 0, 0.2);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  cursor: pointer;
}

.pokemon-card:hover {
  transform: scale(1.05);
  box-shadow: 6px 6px 15px rgba(0, 0, 0, 0.3);
}

.pokemon-card h2 {
  font-size: 20px;
  font-weight: bold;
  text-transform: capitalize;
  color: white;
  margin-bottom: 10px;
}

.pokemon-card img {
  width: 96px;
  height: 96px;
  object-fit: contain;
  margin: 0 auto 10px;
  display: block;
  filter: drop-shadow(2px 2px 4px rgba(0, 0, 0, 0.3));
}

.ability-title {
  font-weight: bold;
  color: white;
  margin-bottom: 5px;
}

.ability-list {
  list-style: none;
  padding: 0;
  display: flex;
  flex-direction: column;
  gap: 5px;
  align-items: center;
}

.ability-list li {
  font-size: 13px;
  color: white;
  background: rgba(255, 255, 255, 0.2);
  padding: 4px 8px;
  border-radius: 8px;
  width: 80%;
  text-align: center;
}
</style>
