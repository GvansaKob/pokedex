<template>
  <div
    class="fixed inset-0 bg-black bg-opacity-60 flex justify-center items-center z-50"
    @click.self="$emit('close')"
  >
    <div class="bg-white rounded-xl p-6 w-[90%] max-w-md shadow-lg relative text-center">
      <!-- Bouton de fermeture -->
      <button
        class="absolute top-2 right-3 text-gray-500 text-xl font-bold hover:text-red-500"
        @click="$emit('close')"
      >
        &times;
      </button>

      <!-- Image + nom -->
      <img
        :src="pokemon.image"
        :alt="pokemon.name"
        class="w-32 h-32 mx-auto mb-4 drop-shadow"
      />
      <h2 class="text-2xl font-bold capitalize text-orange-600 mb-2">
        {{ pokemon.name }}
      </h2>

      <!-- Types -->
      <div class="mb-4">
        <p class="font-semibold text-gray-700">Types :</p>
        <div class="flex flex-wrap justify-center gap-2 mt-1">
          <span
            v-for="type in pokemon.types"
            :key="type.type.name"
            class="px-3 py-1 bg-orange-200 text-orange-700 rounded-full text-sm font-medium"
          >
            {{ type.type.name }}
          </span>
        </div>
      </div>

      <!-- Compétences -->
      <div>
        <p class="font-semibold text-gray-700">Compétences :</p>
        <ul class="mt-2 flex flex-col items-center gap-1">
          <li
            v-for="ability in pokemon.abilities"
            :key="ability.ability.name"
            class="bg-gray-100 px-3 py-1 rounded text-sm text-gray-700 w-3/4 text-center"
          >
            {{ ability.ability.name }}
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'PokemonModal',
  props: {
    pokemon: {
      type: Object,
      required: true
    }
  }
}
</script>
