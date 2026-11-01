<script setup>
import { ref, onMounted } from 'vue'
import PokemonCard from './components/PokemonCard.vue'

const pokemones = ref([])
const pokemoneseleccionado = ref(null)
// generar 40 ids aleatorios sin repetir.
function generarIds() {
  const ids = Array.from({ length: 1025 }, (_, i) => i + 1)

  for (let i = ids.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1))
    ;[ids[i], ids[j]] = [ids[j], ids[i]]
  }

  return ids.slice(0, 40)
}

//consultar la infor
async function getPokemon(id) {
  const response = await fetch(
    `https://pokeapi.co/api/v2/pokemon/${id}`
  )

  if (!response.ok) {
    throw new Error('No se pudo obtener el Pokémon')
  }

  const data = await response.json()

  return {
    id: data.id,
    name: data.name,

    image:
      data.sprites.other?.['official-artwork']?.front_default ||
      data.sprites.front_default,

    hp: data.stats.find(stat => stat.stat.name === 'hp')?.base_stat ?? 0,
    attack: data.stats.find(stat => stat.stat.name === 'attack')?.base_stat ?? 0,
    defense: data.stats.find(stat => stat.stat.name === 'defense')?.base_stat ?? 0,

    types: data.types.map(type => type.type.name),

    abilities: data.abilities
      .slice(0, 2)
      .map(ability => ability.ability.name),

    height: data.height / 10,
    weight: data.weight / 10
  }
}

async function cargarMazo() {
  const ids = generarIds()
  const cargarpokemones = []

  // Se consultan en grupos de 8.
  for (let i = 0; i < ids.length; i += 8) {
    const batch = ids.slice(i, i + 8)

    const results = await Promise.allSettled(
      batch.map(id => getPokemon(id))
    )

    results.forEach(result => {
      if (result.status === 'fulfilled') {
        cargarpokemones.push(result.value)
      }
    })
  }

  pokemones.value = cargarpokemones
}

onMounted(() => {
  cargarMazo()
})

function seleccionarPokemon(pokemon) {
  pokemoneseleccionado.value = pokemon
}

function cerrarCarta() {
  pokemoneseleccionado.value = null
}
</script>

<template>
  <main class="mazo-container">

    <header class="mazo-header">
      <h1>
        POKÉMON <span>Mazo</span>
      </h1>

      <p>{{ pokemones.length }} / 40 CARTAS</p>
    </header>

    <section class="mazo-grid">
      <PokemonCard
        v-for="pokemon in pokemones"
        :key="pokemon.id"
        :pokemon="pokemon"
        @seleccionar="seleccionarPokemon"
      />
    </section>

    <!--modal para mostrar una carta completa porque en la pantalla no se ve completa-->
    <div
    v-if="pokemoneseleccionado"
    class="pokemon-modal"
    @click.self="cerrarCarta"
    >
    <div class="modal-content">
        <button
        class="modal-close"
        @click="cerrarCarta"
        >
        ✕
        </button>

        <PokemonCard
        :pokemon="pokemoneseleccionado"
        :expanded="true"
        />
    </div>
    </div> 

  </main>
</template>