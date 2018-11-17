# Getters

Algunas veces necesitamos hacer cálculos basados en el store state, por ejemplo filtrar a través de una lista de items y contarlos:

``` js
computed: {
  doneTodosCount () {
    return this.$store.state.todos.filter(todo => todo.done).length
  }
}
```

Si mas de un componente necesita hacer uso de esto, tendrás que duplicar la función, o extraerla dentro de un helper compartido e importarlo en multiples lugares - ambas no son ideales.

Vuex nos permite definir "getters" en el store. Podras pensarlo como propiedades computed pero para stores. Como las propiedades computed, el resultado de un getter es cacheado basado en sus dependencias, y solo saran re-evaluados cuando alguna de sus dependencias haya cambiado.

Los getters recibirán el state como su primer argumento:

``` js
const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
})
```

### Estilo de acceso como propiedad

Los getter saran expuestos en el objeto `store.getters`, y podrás acceder a sus valores como propiedades:

``` js
store.getters.doneTodos // -> [{ id: 1, text: '...', done: true }]
```

Los getters también reciben los otros getters como segundo argumento:

``` js
getters: {
  // ...
  doneTodosCount: (state, getters) => {
    return getters.doneTodos.length
  }
}
```

``` js
store.getters.doneTodosCount // -> 1
```

Ahora podemos fácilmente hacer uso de ellos dentro de cualquier componente:

``` js
computed: {
  doneTodosCount () {
    return this.$store.getters.doneTodosCount
  }
}
```

Nota que esos getter que se acceden como propiedades son cacheados como parte de sistema de re-actividad de Vue.

### Estilo de acceso como método

También puedes pasar argumento a los getters retornando una función. Esto puede ser particularmente util cuando tienes que hacer un query en un array del store:

```js
getters: {
  // ...
  getTodoById: (state) => (id) => {
    return state.todos.find(todo => todo.id === id)
  }
}
```

``` js
store.getters.getTodoById(2) // -> { id: 2, text: '...', done: false }
```

Note que esos getters que son accedidos via métodos que serán ejecutados cada vez que tu los llames, y el resultado no sera cacheado.

### El helper `mapGetters`

El helper `mapGetters` simplemente mapea los getters del store en propiedades computed locales:

``` js
import { mapGetters } from 'vuex'

export default {
  // ...
  computed: {
    // mezcla los getters en computed's con el operador objeto spread
    ...mapGetters([
      'doneTodosCount',
      'anotherGetter',
      // ...
    ])
  }
}
```

Si quieres mapear un getter con diferente nombre, puedes usar un objeto:

``` js
...mapGetters({
  // mapea `this.doneCount` a `this.$store.getters.doneTodosCount`
  doneCount: 'doneTodosCount'
})
```
