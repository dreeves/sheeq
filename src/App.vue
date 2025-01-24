<script setup>
import { ref, computed, onMounted } from 'vue'


const equation = ref('')
const result = ref('')
const variables = ref({})
const inferredVariable = ref('')

// Parse URL parameters on load
onMounted(() => {
  const params = new URLSearchParams(window.location.search)
  const urlEquation = params.get('eq')
  const urlVars = params.get('vars')
  const urlInferred = params.get('infer')
  
  if (urlEquation) {
    equation.value = decodeURIComponent(urlEquation)
    if (urlVars) {
      try {
        variables.value = JSON.parse(decodeURIComponent(urlVars))
      } catch (e) {} // Silently fail if JSON is invalid
    }
    if (urlInferred) {
      inferredVariable.value = decodeURIComponent(urlInferred)
    }
    evaluateEquation()
  }
})

const copyUrl = () => {
  const params = new URLSearchParams()
  params.set('eq', encodeURIComponent(equation.value))
  params.set('vars', encodeURIComponent(JSON.stringify(variables.value)))
  if (inferredVariable.value) {
    params.set('infer', encodeURIComponent(inferredVariable.value))
  }
  
  const url = `${window.location.origin}${window.location.pathname}?${params.toString()}`
  navigator.clipboard.writeText(url)
}

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

// Convert ^ to ** for exponents
const convertExponents = (eq) => eq?.replace(/\^/g, '**') || ''

const evaluateEquation = () => {

  try {
    // Split on any number of equals signs
    const sides = equation.value.split(/=+/)
    if (sides.length !== 2) {
      result.value = 'Please enter an equation with = to solve'
      return
    }
    const [leftSide, rightSide] = sides
    const funcDef = `function f(${inferredVariable.value || 'x'}) {
      ${Object.entries(variables.value)
        .filter(([k]) => k !== inferredVariable.value)
        .map(([k,v]) => `var ${k}=${v}`).join(';\n      ')}
      ${inferredVariable.value || 'x'} = Number(${inferredVariable.value || 'x'});
      const result = (${convertExponents(leftSide)}) - (${convertExponents(rightSide)});

      return result;
    }`      // Try to create and test a fresh function for this equation
      let f
      try {
        f = eval(`(${funcDef})`).bind(null)
        // Test the function with a value to make sure it works
        const testValue = f(0)

      function findRoot(f, maxIter=100, tol=1e-7) {
        function bracketPositive() {
          let lower = 0, upper = 1;
          while (Math.sign(f(lower)) === Math.sign(f(upper))) {
            lower = upper;
            upper *= 2;
            if (Math.abs(upper) > Number.MAX_SAFE_INTEGER) return null;
          }
          return [lower, upper];
        }

        function bracketNegative() {
          let upper = 0, lower = -1;
          while (Math.sign(f(lower)) === Math.sign(f(upper))) {
            upper = lower;
            lower *= 2;
            if (Math.abs(lower) > Number.MAX_SAFE_INTEGER) return null;
          }
          return [lower, upper];
        }

        function brent(a, b) {
          let fa = f(a), fb = f(b), c = a, fc = fa, d = b - a, e = d;
          
          for (let i = 0; i < maxIter; i++) {
            if (Math.abs(fc) < Math.abs(fb)) {
              [a, b] = [b, a];
              [fa, fb] = [fb, fa];
              [c, fc] = [a, fa];
            }
            
            const m = 0.5 * (c - b);
            if (Math.abs(m) < tol || fb === 0) return b;
            
            if (Math.abs(e) < tol || Math.abs(fa) <= Math.abs(fb)) {
              d = e = m;
            } else {
              let s = fb / fa, p, q;
              if (a === c) {
                p = 2 * m * s;
                q = 1 - s;
              } else {
                q = fa / fc; let r = fb / fc;
                p = s * (2 * m * q * (q - r) - (b - a) * (r - 1));
                q = (q - 1) * (r - 1) * (s - 1);
              }
              if (p > 0) q = -q;
              p = Math.abs(p);
              
              if (2 * p < Math.min(3 * m * q - Math.abs(tol * q), Math.abs(e * q))) {
                e = d; d = p / q;
              } else {
                d = e = m;
              }
            }
            
            a = b; fa = fb;
            if (Math.abs(d) > tol) b += d; else b += (m > 0 ? tol : -tol);
            fb = f(b);
            
            if ((fb > 0 && fc > 0) || (fb < 0 && fc < 0)) {
              c = a; fc = fa; e = d = b - a;
            }
          }
          return b;
        }

        let bracket = bracketPositive() || bracketNegative();
        if (!bracket) return null;
        return brent(bracket[0], bracket[1]);
      }

      const root = findRoot(f);
      if (root === null) {
        result.value = 'Could not find a solution. Try different variable values.'
        return
      }
      variables.value[inferredVariable.value] = root
      result.value = `${inferredVariable.value} = ${root}`
    } catch (e) {

      result.value = 'Could not solve equation. Check that your equation is valid.'
    }
  } catch (error) {

    result.value = 'Error evaluating equation. Please check your equation format.'
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
      placeholder="a^2 + b^2 = c^2"
      @keyup.enter="evaluateEquation"
      spellcheck="false"
    ></textarea>
    
    <div v-if="variableNames.length" class="variables">
      <div v-for="varName in variableNames" :key="varName" class="variable-row">
        <input 
          type="radio" 
          :id="'radio-' + varName"
          :value="varName"
          v-model="inferredVariable"
          name="inferred-var"
        >
        <input 
          :id="'input-' + varName"
          type="number" 
          :value="typeof variables[varName] === 'number' ? Number(variables[varName].toFixed(6)) : variables[varName]"
          @input="e => { variables[varName] = Number(e.target.value); evaluateEquation(); }"
          :disabled="inferredVariable === varName"
          :placeholder="inferredVariable === varName ? 'Solving...' : ''"
        >
        <label :for="'radio-' + varName">{{ varName.replace(/_/g, ' ') }}</label>
      </div>    </div>
    
    <button 
      class="share-button"
      @click="copyUrl"
    >
      Copy URL To Share This
    </button>
  </div>
</template>

<style scoped>
.equation-calculator {
  max-width: 600px;
  margin: 3rem auto;
  padding: 2rem;
  background: white;
  border-radius: 12px;
  box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1);
}

textarea {
  width: 100%;
  min-height: 100px;
  margin-bottom: 1.5rem;
  padding: 1rem;
  border: 2px solid #e5e7eb;
  border-radius: 8px;
  font-family: monospace;
  font-size: 1.1rem;
  resize: vertical;
  transition: border-color 0.15s ease;
}

textarea:focus {
  outline: none;
  border-color: #60a5fa;
  box-shadow: 0 0 0 3px rgb(96 165 250 / 0.2);
}

.variables {
  display: grid;
  gap: 1rem;
  margin: 1.5rem 0;
  padding: 1.5rem;
  background: #f9fafb;
  border-radius: 8px;
}

.variable-row {
  display: grid;
  grid-template-columns: auto auto 1fr;
  align-items: center;
  gap: 1rem;
}

.variable-row label {
  font-size: 1.1rem;
  color: #374151;
}

.variable-row input[type="number"] {
  padding: 0.5rem;
  border: 1px solid #e5e7eb;
  border-radius: 6px;
  width: 120px;
  font-size: 1rem;
}

.variable-row input[type="number"]:disabled {
  background: #f3f4f6;
  border-color: #d1d5db;
}.variable-row input[type="radio"] {
  width: 1.2rem;
  height: 1.2rem;
  accent-color: #60a5fa;
}

.result:empty {
  display: none;
}

.share-button {
  display: block;
  margin: 1.5rem auto 0;
  padding: 0.5rem 1rem;
  background: #60a5fa;
  color: white;
  border: none;
  border-radius: 6px;
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.15s;
}

.share-button:hover {
  background: #3b82f6;
}

.share-button:active {
  background: #2563eb;
}

</style>
