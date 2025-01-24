<script setup>
import { ref, computed, onMounted } from 'vue'
import fzero from 'fzero'

const equation = ref('(caloriesPerGramSugar*healthyGramsSugarPerServing*gramsHealthy/healthyGramsPerServing + caloriesPerGramBrownSugar*gramsBrownSugarToAdd)/(healthyCaloriesPerServing*gramsHealthy/healthyGramsPerServing + caloriesPerGramBrownSugar*gramsBrownSugarToAdd) == caloriesPerGramSugar*junkGramsSugarPerServing/junkCaloriesPerServing')
const result = ref('')
const variables = ref({
  caloriesPerGramSugar: 3.87,
  gramsHealthy: 233.5,
  healthyGramsSugarPerServing: 5,
  junkCaloriesPerServing: 150,
  healthyCaloriesPerServing: 120,
  caloriesPerGramBrownSugar: 3.8,
  healthyGramsPerServing: 170,
  junkGramsSugarPerServing: 23,
  gramsBrownSugarToAdd: 0  // This will be inferred
})
const inferredVariable = ref('gramsBrownSugarToAdd')

const variableNames = computed(() => {
  const matches = equation.value.match(/[\p{L}_][\p{L}0-9_]*/gu) || []
  const uniqueVars = [...new Set(matches)].filter(v => v !== 'true' && v !== 'false')
  
  // Initialize any new variables with default value 0
  uniqueVars.forEach(varName => {
    if (!(varName in variables.value)) {
      variables.value[varName] = 0
    }
  })
  
  // Set default inferred variable if none selected
  if (!inferredVariable.value && uniqueVars.length > 0) {
    inferredVariable.value = uniqueVars[0]
  }
  
  return uniqueVars
})

const evaluateEquation = () => {
  try {
    // Create function definition string
    const funcDef = `function f(${inferredVariable.value}) { 
      const ${Object.entries(variables.value)
        .filter(([k]) => k !== inferredVariable.value)
        .map(([k,v]) => `${k}=${v}`).join(',')};
      ${inferredVariable.value} = Number(${inferredVariable.value});  // Convert string input to number
      return ((${equation.value.split('==')[0]}) - (${equation.value.split('==')[1]})).toString();  // Return string for decimal.js
    }`
    console.log('Function definition:', funcDef)
    
    // Evaluate the function definition and capture the function
    const f = eval(`(${funcDef})`).bind(null)
    
    // Use appropriate bracketing points based on which variable we're solving for
    const brackets = {
      gramsBrownSugarToAdd: [40, 50],
      gramsHealthy: [200, 300],
      healthyGramsSugarPerServing: [1, 10],
      junkCaloriesPerServing: [100, 200],
      healthyCaloriesPerServing: [100, 200],
      healthyGramsPerServing: [100, 200],
      junkGramsSugarPerServing: [10, 30]
    };

    const solution = fzero(f, brackets[inferredVariable.value] || [0, 100]);
    
    console.log('Solution details:', solution);

    // Get the precise solution from the diagnostic info
    if (!solution.diagnostic?.bracketx) {
      throw new Error('Failed to find solution');
    }

    // The solution is between the final bracket points
    const [a, b] = solution.diagnostic.bracketx;
    const root = (parseFloat(a) + parseFloat(b)) / 2;
    
    // Update the inferred variable's value with what we found
    variables.value[inferredVariable.value] = root
    result.value = `${inferredVariable.value} = ${variables.value[inferredVariable.value]}`
  } catch (error) {
    console.error('Error:', error)
    result.value = 'Error evaluating equation'
  }
}

onMounted(() => {
  evaluateEquation()
})
</script>

<template>
  <div class="equation-calculator">
    <textarea 
      v-model="equation"
      placeholder="Enter your equation (e.g. 2 + 2)"
      @keyup.enter="evaluateEquation"
    ></textarea>
    
    <div v-if="variableNames.length" class="variables">
      <div v-for="varName in variableNames" :key="varName" class="variable-input">
        <div class="variable-row">
          <input 
            type="number" 
            v-model.number="variables[varName]"
            @input="evaluateEquation"
            :disabled="inferredVariable === varName"
          >
          <input 
            type="radio" 
            :id="'radio-' + varName"
            :value="varName"
            v-model="inferredVariable"
            name="inferred-var"
          >
          <label :for="'radio-' + varName">{{ varName }}</label>
        </div>
      </div>
    </div>

    <div v-if="result" class="result">
      {{ result }}
    </div>
  </div>
</template>

<style scoped>
.equation-calculator {
  max-width: 600px;
  margin: 2rem auto;
  padding: 1rem;
}

textarea {
  width: 100%;
  min-height: 100px;
  margin-bottom: 1rem;
  padding: 0.5rem;
}

.variables {
  margin: 1rem 0;
}

.variable-input {
  margin: 0.5rem 0;
}

.variable-row {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.variable-row label {
  min-width: 100px;
}

.variable-row input[type="number"] {
  padding: 0.3rem;
  width: 100px;
}

.variable-row input[type="radio"] {
  margin: 0;
}

.result {
  margin-top: 1rem;
  padding: 1rem;
  background-color: #f5f5f5;
  border-radius: 4px;
}
</style>
