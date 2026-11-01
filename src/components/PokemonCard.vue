<script setup>
const props = defineProps({
  pokemon: {
    type: Object,
    required: true
  },
  expanded: {
    type: Boolean,
    default: false
  }
})

const emit = defineEmits(['seleccionar'])

const typeNames = {
  normal: 'Normal',
  fire: 'Fuego',
  water: 'Agua',
  grass: 'Planta',
  electric: 'Eléctrico',
  ice: 'Hielo',
  fighting: 'Lucha',
  poison: 'Veneno',
  ground: 'Tierra',
  flying: 'Volador',
  psychic: 'Psíquico',
  bug: 'Bicho',
  rock: 'Roca',
  ghost: 'Siniestro',
  dragon: 'Dragón',
  dark: 'Siniestro',
  steel: 'Acero',
  fairy: 'Hada'
}

function translateType(type) {
  return typeNames[type] || type
}
</script>

<template>
  <article
    class="pokemon-card"
    :class="[
        `type-${pokemon.types[0] || 'normal'}`,
        { 'card-expanded': expanded }
    ]"
    @click="!expanded && emit('seleccionar', pokemon)"
  >
    <div class="card-inner">
      <!-- Nombre y puntos de salud -->
      <div class="card-header">
        <h3>{{ pokemon.name }}</h3>
        <strong>HP {{ pokemon.hp }}</strong>
      </div>

      <!-- Tipos e identificador -->
      <div class="card-information">
        <div class="type-list">
          <span
            v-for="type in pokemon.types"
            :key="type"
            class="type-badge"
            :class="`type-${type}`"
          >
            {{ translateType(type) }}
          </span>
        </div>

        <span class="pokemon-id">
          #{{ String(pokemon.id).padStart(3, '0') }}
        </span>
      </div>

      <div class="art-window">
        <img
          v-if="pokemon.image"
          :src="pokemon.image"
          :alt="pokemon.name"
          class="pokemon-art"
        />
      </div>
      <!-- Estadísticas -->
      <div class="card-stats">
        <span>ATQ {{ pokemon.attack }}</span>
        <span>DEF {{ pokemon.defense }}</span>
      </div>
      <!-- Medidas -->
      <div class="card-measures">
        <span>ALT {{ pokemon.height }} m</span>
        <span>PESO {{ pokemon.weight }} kg</span>
      </div>
      <!-- Habilidades -->
      <div class="card-abilities">
        <strong>HABILIDADES</strong>

        <div
          v-for="(ability, index) in pokemon.abilities"
          :key="ability"
          class="ability"
        >
          <span class="ability-number">{{ index + 1 }}</span>
          {{ ability }}
        </div>
      </div>

    </div>
  </article>
</template>