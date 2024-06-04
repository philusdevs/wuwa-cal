<template>
  <div class="bg-gray-100 min-h-screen flex flex-col items-center py-12">
    <div class="bg-white shadow-md rounded-lg p-8 w-full max-w-4xl">
      <h1 class="text-3xl font-bold mb-8">Wuthering Waves Damage Calculator</h1>
      <form @submit.prevent="calculateDamage" v-if="characters.length">
        <div class="mb-4">
          <label for="character" class="block text-sm font-medium text-gray-700">Character:</label>
          <select v-model="selectedCharacter" @change="updateCharacterStats"
            class="mt-1 block w-full px-3 py-2 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm">
            <option v-for="character in characters" :key="character.name" :value="character.name">
              {{ character.name }}
            </option>
          </select>
        </div>
        <div class="mb-4">
          <label for="level" class="block text-sm font-medium text-gray-700">Level:</label>
          <input type="number" v-model.number="currentStats.level"
            class="mt-1 block w-full px-3 py-2 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            required />
        </div>
        <div v-if="selectedCharacterStats.scalingStat === 'atk'" class="mb-4">
          <label for="atk" class="block text-sm font-medium text-gray-700">ATK:</label>
          <input type="number" step="0.01" v-model.number="currentStats.scalingStatValue"
            class="mt-1 block w-full px-3 py-2 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            required />
        </div>
        <div v-else class="mb-4">
          <label for="def" class="block text-sm font-medium text-gray-700">DEF:</label>
          <input type="number" step="0.01" v-model.number="currentStats.scalingStatValue"
            class="mt-1 block w-full px-3 py-2 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            required />
        </div>
        <div class="mb-4">
          <label for="critRate" class="block text-sm font-medium text-gray-700">Crit Rate (%):</label>
          <input type="number" step="0.01" v-model.number="currentStats.critRate"
            class="mt-1 block w-full px-3 py-2 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            required />
        </div>
        <div class="mb-4">
          <label for="critDmg" class="block text-sm font-medium text-gray-700">Crit DMG (%):</label>
          <input type="number" step="0.01" v-model.number="currentStats.critDmg"
            class="mt-1 block w-full px-3 py-2 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            required />
        </div>
        <button type="submit"
          class="mt-4 inline-flex items-center px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
          Calculate Damage
        </button>
      </form>
      <div v-if="results && results.length" class="mt-8">
        <h2 class="text-2xl font-semibold mb-4">Results</h2>
        <div class="overflow-x-auto">
          <table class="min-w-full divide-y divide-gray-200">
            <thead class="bg-gray-50">
              <tr>
                <th scope="col"
                  class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Skill
                </th>
                <th scope="col"
                  class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Non-Crit Damage
                </th>
                <th scope="col"
                  class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Crit Damage
                </th>
                <th scope="col"
                  class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Average Damage
                </th>
              </tr>
            </thead>
            <tbody class="bg-white divide-y divide-gray-200">
              <tr v-for="(result, index) in results" :key="index"
                :class="{'bg-yellow-100': index < 4}">
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">{{ result.skill }}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ result.nonCritDamage }}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ result.critDamage }}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ result.averageDamage }}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
      <div v-else-if="results && !results.length" class="mt-8">
        <p class="text-lg text-gray-500">No results found.</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selectedCharacter: '',
      characters: [],
      currentStats: {
        level: 0,
        atk: 0,
        def: 0,
        scalingStatValue: 0,
        critRate: 0,
        critDmg: 0,
        scalingStat: ''
      },
      results: null
    };
  },
  computed: {
    sortedResults() {
      if (!this.results) return [];
      return [...this.results].sort((a, b) => a.priority - b.priority);
    },
    highestCritDamageResults() {
      if (!this.results) return [];
      return [...this.results]
        .sort((a, b) => parseFloat(b.critDamage) - parseFloat(a.critDamage))
        .slice(0, 4);
    },
    selectedCharacterStats() {
      return this.characters.find(char => char.name === this.selectedCharacter);
    }
  },
  methods: {
    async fetchCharacters() {
      try {
        const response = await fetch('characters.json');
        this.characters = await response.json();
        this.selectedCharacter = this.characters[0]?.name || '';
        this.updateCharacterStats();
      } catch (error) {
        console.error('Error fetching characters:', error);
      }
    },
    updateCharacterStats() {
      const selected = this.characters.find(char => char.name === this.selectedCharacter);
      if (selected) {
        this.currentStats = {
          level: selected.stats.level || 0,
          atk: selected.stats.atk || 0,
          def: selected.stats.def || 0,
          scalingStatValue: selected.stats.atk || 0, // default to atk value
          critRate: selected.stats.critRate || 0,
          critDmg: selected.stats.critDmg || 0,
          scalingStat: selected.scalingStat || ''
        };
      }
    },
    calculateDamage() {
      const selected = this.characters.find(char => char.name === this.selectedCharacter);
      if (selected) {
        this.results = selected.skills.map(skill => {
          let baseDmg = 0;
          let scalingStatValue = this.currentStats.scalingStatValue;
          if (selected.scalingStat === 'def') {
            scalingStatValue = this.currentStats.def;
          }
          baseDmg = parseFloat(scalingStatValue) * (skill.mv / 100);
          const defMultiplier = (800 + this.currentStats.level * 8) / ((792 + this.currentStats.level) + (800 + this.currentStats.level * 8));
          const nonCritDamage = baseDmg * defMultiplier;
          const critDamage = nonCritDamage * (this.currentStats.critDmg / 100);
          const averageDamage = nonCritDamage * (1 - this.currentStats.critRate / 100) + critDamage * (this.currentStats.critRate / 100);
          return {
            skill: skill.name,
            nonCritDamage: nonCritDamage.toFixed(2),
            critDamage: critDamage.toFixed(2),
            averageDamage: averageDamage            .toFixed(2),
            priority: skill.priority
          };
        });

        // Determine the highest crit damage
        const highestCritDamageResults = this.results
          .sort((a, b) => parseFloat(b.critDamage) - parseFloat(a.critDamage))
          .slice(0, 4);

        // Highlight the top 4 highest crit damage results
        this.results.forEach(result => {
          result.highlight = highestCritDamageResults.includes(result);
        });
      }
    }
  },
  mounted() {
    this.fetchCharacters();
  }
};
</script>

<style>
/* Tailwind CSS classes */
@tailwind base;
@tailwind components;
@tailwind utilities;

/* Additional custom styling */
</style>

