# Sheeq: SpreadsHeEt-style interfacE via an eQuation

AKA a calculator calculator.

This is now subsumed by Reciplier: <https://reciplier.dreev.es>

TODO: Add legacy redirects for these URLs:

1. [Sugar Calculator](https://sheeq.dreev.es/?eq=%28Calories_per_gram_of_sugar%2520*%2520%250AGrams_of_sugar_per_serving_in_healthy_stuff%2520*%2520%250AGrams_of_healthy_stuff%2520%252F%2520%250AGrams_per_serving_in_healthy_stuff%2520%252B%2520%250ACalories_per_gram_of_brown_sugar%2520*%2520%250AGrams_of_brown_sugar_to_add%2520%29%2520%252F%2520%28%250ACalories_per_serving_in_healthy_stuff%2520*%2520%250AGrams_of_healthy_stuff%2520%252F%2520%250AGrams_per_serving_in_healthy_stuff%2520%252B%2520%250ACalories_per_gram_of_brown_sugar%2520*%2520%250AGrams_of_brown_sugar_to_add%2520%29%2520%250A%253D%253D%2520%250ACalories_per_gram_of_sugar%2520*%2520%250AGrams_of_sugar_per_serving_in_junk_food%2520%252F%2520%250ACalories_per_serving_in_junk_food&vars=%257B%2522Calories_per_gram_of_sugar%2522%253A3.87%252C%2522Grams_of_sugar_per_serving_in_healthy_stuff%2522%253A5%252C%2522Grams_of_healthy_stuff%2522%253A233.5%252C%2522Grams_per_serving_in_healthy_stuff%2522%253A170%252C%2522Calories_per_gram_of_brown_sugar%2522%253A3.8%252C%2522Grams_of_brown_sugar_to_add%2522%253A46.10019431698941%252C%2522Calories_per_serving_in_healthy_stuff%2522%253A120%252C%2522Grams_of_sugar_per_serving_in_junk_food%2522%253A23%252C%2522Calories_per_serving_in_junk_food%2522%253A150%257D&infer=Grams_of_brown_sugar_to_add) -> TODO

2. [Pounds<->Kilograms Calculator](https://sheeq.dreev.es/?eq=pounds%2520%253D%2520kilograms%2520%252F%25200.45359237&vars=%257B%2522p%2522%253A0%252C%2522po%2522%253A0%252C%2522pou%2522%253A0%252C%2522poun%2522%253A0%252C%2522pound%2522%253A0%252C%2522pounds%2522%253A154.3235835294143%252C%2522k%2522%253A0%252C%2522ki%2522%253A0%252C%2522kil%2522%253A0%252C%2522kilo%2522%253A0%252C%2522kilob%2522%253A0%252C%2522kilog%2522%253A0%252C%2522kilogr%2522%253A0%252C%2522kilogra%2522%253A0%252C%2522kilogram%2522%253A0%252C%2522kilograms%2522%253A70%257D&infer=pounds) -> TODO

3. David Yang's monitor resolution calculator if I can find that.


Background 
[on the Beeminder forum](https://forum.beeminder.com/t/turning-greek-yogurt-into-junk-food/12184/2?u=dreev).

Implemented by Codebuff, using 
[Narthur's tool](https://codebuff.nathanarthur.com/?step=4&pm=bun&fw=vue&ts=true&deploy=render).

PS: I intend for this thing to be subsumed by The Reciplier.

BUG: It needs to clear the inferred field as soon as any field changes.  
Replicata: a^2+b^2=c^2, infer a, put b=12 and c=13, so a shows correctly as 5. Then change c to 11. There's no longer a real value of a that makes the equation true.  
Expectata: Show an error or a blank field for a.  
Resultata: Still shows a=5.

# Hosting

Hosted on GitHub Pages, no backend.

<https://sheeq.dreev.es>

<br>&nbsp;<br>

[Boilerplate from Vue and Vite or whatever follows]

This template should help get you started developing with Vue 3 in Vite.

### Recommended IDE Setup

[VSCode](https://code.visualstudio.com/) + [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur).

### Customize configuration

See [Vite Configuration Reference](https://vite.dev/config/).

### Project Setup

```sh
npm install
```

#### Compile and Hot-Reload for Development

```sh
npm run dev
```

#### Compile and Minify for Production

```sh
npm run build
```
