# Sheeq: SpreadSHeet-style interface via an EQuation

AKA a calculator calculator.

Background 
[on the Beeminder forum](https://forum.beeminder.com/t/turning-greek-yogurt-into-junk-food/12184/2?u=dreev).

Implemented by Codebuff, using 
[Narthur's tool](https://codebuff.nathanarthur.com/?step=4&pm=bun&fw=vue&ts=true&deploy=render).

BUG: It needs to clear the inferred field as soon as any field changes.  
Replicata: a^2+b^2=c^2, infer a, put b=12 and c=13, so a shows correctly as 5. Then change c to 11. There's no longer a real value of a that makes the equation true.  
Expectata: Show an error or a blank field for a.  
Resultata: Still shows a=5.

# Hosting

Hosted on Replit, no backend.

<https://sheeq.replit.app>

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
