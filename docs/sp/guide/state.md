# State

### Árbol de un solo state

Vuex usa un **árbol de un solo state** - esto es, un solo objeto contiene todo el nivel del state de tu aplicación y sirve como "la única fuente de la verdad". Usualmente también significa que tendrás solo un store para cada aplicación. Un árbol de un solo state hace mas fácil localizar una parte especifica del state, y nos permite tomar fácilmente snapshots del state del app para propósitos de depuración.

El árbol de un solo state no conflictúa con la modularidad - en próximos capítulos discutiremos como separar tu state y sus mutaciones en sub módulos.

### Comenzando con el Vuex State en Componentes Vue

Entonces, como mostramos el state dentro del store en nuestros componentes Vue? Desde que el store de Vuex es reactivo, la manera simple de recuperar el state es simplemente retornar algún store state desde una [propiedad computed](https://vuejs.org/guide/computed.html):

``` js
// creamos un componente Counter
const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: {
    count () {
      return store.state.count
    }
  }
}
```

Cada vez que `store.state.count` cambie, esto hará que la propiedad computed se re-evalué, y desencadene las actualizaciones asociadas al DOM.

Sin embargo, este patron causa que el complemento dependa del singleton del store global. Cuando usamos un sistema modulado, requiere importar el store en cada componente que use el store state, y también requiere mocking cuando testeamos el componente.

Vuex provee un mecanismo para inyectar el store en cada componente hijo del componente principal con la opción `store` (habilitado por `Vue.use(Vuex)`):

``` js
const app = new Vue({
  el: '#app',
  // provee el store usando la opcion "store".
  // esto inyectara la instancia del store en todos los componentes hijo.
  store,
  components: { Counter },
  template: `
    <div class="app">
      <counter></counter>
    </div>
  `
})
```

Proveyendo la opción `store` a la instancia principal, el store sera inyectado en todos los componentes hijo del componente principal y sera accesible mediante `this.$store`. Ahora actualizaremos nuestra implementación del `Counter`:

``` js
const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: {
    count () {
      return this.$store.state.count
    }
  }
}
```

### El Helper `mapState`

Cuando un componente necesite hacer uso de multiples propiedades del store state o getters, declarar todas esas propiedades computed puede ser repetitivo y verboso. Para solucionar eso podemos hacer uso del helper `mapState` que genera funciones computed getter por nosotros, librándonos del algunos tecleos:

``` js
// en helpers completamente construidos son expuestos como Vuex.mapState
import { mapState } from 'vuex'

export default {
  // ...
  computed: mapState({
    // las funciones arrow pueden hacer el código bastante corto!
    count: state => state.count,

    // pasando el valor string 'count' es lo mismo que `state => state.count`
    countAlias: 'count',

    // para acceder al state local con `this`, se debe usar una función normal
    countPlusLocalState (state) {
      return state.count + this.localCount
    }
  })
}
```

También podemos pasar un string array a `mapState` con el nombre de la propiedad computed mapeada que es la misma que el sub nombre que esta en el arbol del state.

``` js
computed: mapState([
  // mapea this.count en store.state.count
  'count'
])
```

### El Operador de Objecto Spread

Note que `mapState` retorna un objeto. Como podemos hacer uso de el en combinación con otras propiedades computed locales? Normalmente, tenemos que usar la utilidad para juntar multiples objetos en uno solo entonces podemos pasar el objeto final a `computed`. Sin embargo con el [operador de objeto spread](https://github.com/sebmarkbage/ecmascript-rest-spread) (que es una propuesta stage-4 de ECMAScript), podemos gratamente simplificar la sintaxis:

``` js
computed: {
  localComputed () { /* ... */ },
  // mezcla esto con otro objecto de afuera con el operador de objeto spread
  ...mapState({
    // ...
  })
}
```

### Los Componentes pueden seguir teniendo un state local

Usando Vuex no significa que tengas que poner **todo** el state en Vuex. A través de poner mas state dentro de Vuex hace que las mutaciones del state sean mas explicitas y depurables, algunas veces puede hacer el código mas verboso e indirecto. Si una pieza del state estrictamente pertenece a un solo componente, puede estar bien solo dejarlo como un state local. Podrías pesar el intercambio y tomar decisiones que adelgacen el desarrollo que necesite tu aplicación.
