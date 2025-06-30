<template>
  <div
    class="fixed inset-0 bg-black bg-opacity-60 flex justify-center items-center z-50"
    @click.self="$emit('close')"
  >
    <div class="bg-white rounded-xl p-6 w-[90%] max-w-2xl shadow-lg relative">
      <button
        class="absolute top-2 right-3 text-gray-500 text-xl font-bold hover:text-red-500"
        @click="$emit('close')"
      >
        &times;
      </button>

      <h2 class="text-2xl font-bold text-center text-orange-600 mb-4">Mon Pokédex</h2>

      <div v-if="pokedex.length === 0" class="text-center text-gray-500">
        Aucun Pokémon ajouté.
      </div>

      <div v-else class="grid grid-cols-2 sm:grid-cols-3 gap-4">
        <div
          v-for="pokemon in pokedex"
          :key="pokemon.name"
          class="bg-orange-100 rounded-xl p-4 text-center shadow"
        >
          <img :src="pokemon.image" :alt="pokemon.name" class="w-16 h-16 mx-auto mb-2" />
          <h3 class="capitalize font-semibold text-orange-700">{{ pokemon.name }}</h3>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'PokedexModal',
  props: {
    pokedex: {
      type: Array,
      required: true
    }
  }
}
</script>
