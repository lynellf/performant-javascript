# performant-javascript
Best practices for performant (and clean?) JavaScript. Totally not subjective. At all.

Recursion vs Higher Order Array Methods vs Loops
===
```js
const first = xs => xs[0];
const rest = xs => xs.slice(1);
const reducer = (arr, cb, output) => {
    return arr.length > 0 ? reducer(rest(arr), cb, cb(first(arr), output)) : output;
};
const keys = Array.from(Array(10000000).keys());
const values = keys;
function mapValues(key, output) {
    output[key] = values[key];
    return output;
}
// Slowest: Will cause an error in chrome at around ~1000 iterations
const testDict = reducer(keys, mapValues, {});
// Second Fastest: at around 0.444 seconds with 1000000 iterations
const altTestDict = keys.reduce((output, key) => mapValues(key, output), {});
// Fastest: at around 0.293 seconds with 1000000 iterations
const output = {};
const arrLength = keys.length;
for (let i = 0; i < arrLength; i++) {
    output[i] = values[i];
}
```
![Totally not trolling](https://i.imgur.com/JACJCth.png)

Handling Large Conditional Statements (Table Lookup vs Switch vs If/Else)
===

> We normally use switch statements to avoid large if-else if statements. However, switch statements are very verbose, hard to maintain and even harder to debug. - [Estefanía García Gallardo](https://medium.com/better-programming/the-art-of-refactoring-5-tips-to-write-better-code-3bc1f6f7689)

### Switch

```js
function getPokemon(type) {
  let pokemon;
  switch (type) {
    case 'Water':
      pokemon = 'Squirtle';
      break;
    case 'Fire':
      pokemon = 'Charmander';
      break;
    case 'Plant':
      pokemon = 'Bulbasur';
      break;
    case 'Electric':
      pokemon = 'Pikachu';
      break;
    default:
      pokemon = 'Mew';
  }
  return pokemon;
}

console.log(getPokemon('Fire')); // Result: Charmander
```

### Object Literal Lookup
```js
const pokemon = {
    Water: 'Squirtle',
    Fire: 'Charmander',
    Plant: 'Bulbasur',
    Electric: 'Pikachu'
  };

function getPokemon(type) {
  return pokemon[type] || 'Mew';
}
console.log(getPokemon('Fire')); // Result: Charmander

// If the type isn't found in the pokemon object, the function will return the default value 'Mew'
console.log(getPokemon('unknown')); // Result: Mew
```

### Map Lookup
```js
const pokemon = new Map([
    ['Water', 'Squirtle'],
    ['Fire', 'Charmander'],
    ['Plant', 'Bulbasur'],
    ['Electric', 'Pikachu']
  ]);

function getPokemon(type) {
  return pokemon.get(type) || 'Mew';
}

console.log(getPokemon('Fire')); // Result: Charmander
console.log(getPokemon('unknown')); // Result: Mew
```

### Possibly no longer relevant (Internet Explorer 7???)
![May be old](https://i.imgur.com/KeXxiWF.png)

Either way, a table lookup appears to be O(1), whereas the other approaches are not.
