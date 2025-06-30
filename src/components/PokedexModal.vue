<template>
  <div class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
    <div class="bg-white rounded-lg shadow-lg p-6 max-w-3xl w-full relative">
      <!-- Titre -->
      <h2 class="text-2xl font-bold text-orange-600 mb-4">Mon Pokédex</h2>

      <!-- Liste de pokémons -->
      <div v-if="pokedex.length > 0" class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-4">
        <div
          v-for="pokemon in pokedex"
          :key="pokemon.name"
          class="relative border border-orange-300 rounded-lg p-4 bg-orange-100 shadow"
        >
          <!-- Bouton suppression -->
          <button
            class="absolute top-2 right-2 text-red-600 font-bold text-xl hover:text-red-800"
            @click="$emit('remove', pokemon.name)"
            title="Supprimer"
          >
            ×
          </button>

          <img :src="pokemon.image" alt="pokemon" class="mx-auto mb-2 w-20 h-20 object-contain" />
          <h3 class="text-center font-semibold text-lg capitalize">{{ pokemon.name }}</h3>
        </div>
      </div>

      <p v-else class="text-gray-600 text-center py-8">Aucun Pokémon ajouté pour l’instant.</p>

      <!-- Bouton fermer -->
      <div class="text-center mt-6">
        <button
          @click="$emit('close')"
          class="bg-orange-500 hover:bg-orange-600 text-white font-bold py-2 px-6 rounded-full transition"
        >
          Fermer
        </button>
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
