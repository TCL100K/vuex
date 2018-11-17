# Mutaciones

La única manera posible de actualizar un cambio del state en una store Vuex es commiteando una mutación. Las mutaciones Vuex o _mutations_ son bastante similares a los eventos: cada mutación tiene un **tipo** string y un **handler**. La función handler es la que hace las modificaciones en el state, y recibirá el state como primer argumento:

``` js
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // mutate state
      state.count++
    }
  }
})
```

Tu no puedes directamente llamar un handler de un mutador directamente. Piénsalo mas como un registrado de eventos: "Cuando una mutación con el tipo `increment` es desencadenado, llama ese handler." Para invocar un handler de un mutador, tienes que llamar a `store.commit` con su tipo:

``` js
store.commit('increment')
```

### Commitear con Payload

Puedes pasar un argumento adicional a `store.commit`, que es llamado el **payload** o _carga_ para la mutación:

``` js
// ...
mutations: {
  increment (state, n) {
    state.count += n
  }
}
```
``` js
store.commit('increment', 10)
```

En la mayoría de los casos, el payload sera un objecto, entonces puede contener varias filas, y el record de la mutación también sera mas descriptivo:

``` js
// ...
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}
```

``` js
store.commit('increment', {
  amount: 10
})
```

### Estilo de commiteo como objeto

Una alternativa para commitear una mutación es directamente usando un objecto con la propiedad `type`:

``` js
store.commit({
  type: 'increment',
  amount: 10
})
```

Cuando usamos el estilo de commieto como objecto, el objecto entero sera pasado como payload para los handlers de los mutadores, entonces el handler permanece el mismo:

``` js
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}
```

### Los mutadores siguen las reglas de re-actividad de Vue

Desde que los store state de Vuex fueron hechos reactivos por Vue, cuando mutamos el state, los componentes Vue observan que el state se a actualizado automáticamente. Esto también significa que las mutaciones son sujeto de las misma re-actividad cuando trabajamos con Vue plano:

1. Preferir inicializar tu store state inicial con todos sus filas al frente.

2. Cuando añadimos nuevas propiedades a un objeto puedes:

  - Usar `Vue.set(obj, 'newProp', 123)`, o

  - Reemplazar el objeto por uno fresco. Por ejemplo, usando la [sintaxis de objeto spread](https://github.com/sebmarkbage/ecmascript-rest-spread) podemos escribir algo como esto:

    ``` js
    state.obj = { ...state.obj, newProp: 123 }
    ```

### Usar constantes para los tipos de mutación

Es un patron bastante común el de usar constantes para los tipos de mutación en varias implementaciones de Flux. Esto permite al código tomar ventaja de herramientas como linters, y poner todas las constantes en un solo archivo que permita a tus colaboradores obtener una vistazo de las mutaciones que se aplicaran posiblemente en toda la aplicación:

``` js
// mutation-types.js
export const SOME_MUTATION = 'SOME_MUTATION'
```

``` js
// store.js
import Vuex from 'vuex'
import { SOME_MUTATION } from './mutation-types'

const store = new Vuex.Store({
  state: { ... },
  mutations: {
    // podemos usar la característica computad property de ES2015
    // para usar una constante como el nombre de la función
    [SOME_MUTATION] (state) {
      // mutar state
    }
  }
})
```

Aunque usar constantes es una preferencia - puede ser util en proyectos largos con muchos desarrolladores, pero es totalmente opcional si no te gustan.

### Las mutaciones deben ser síncronas

Una regla importante a recordar es que **las funciones de los handlers de los mutadores deben ser síncronos**. Por que? Considera el siguiente ejemplo:

``` js
mutations: {
  someMutation (state) {
    api.callAsyncMethod(() => {
      state.count++
    })
  }
}
```

Ahora imagina que estamos depurando una app en busca de los de mutación en el devtool. En cada mutación loggeada, el devtoool necesitara capturar snapshots "antes" y "despues" del state. Sin embargo, un callback asíncrono dentro del ejemplo de mutación debajo lo hará imposible: el callback no es llamado aun cuando la mutación es commiteada, y no hay manera que el devtool sepa cuando el callback sea llamado - cualquier mutación hecha en el callback hará que sea esencialmente in-traceable!

### Commiteando mutaciones en Componentes

Puedes commitear mutaciones en componentes con `this.$store.commit('xxx')`, o usar el helper `mapMutations` que puede mapear los métodos del componente a llamadas store.commit` (require la inyección del store en la instancia principal):

``` js
import { mapMutations } from 'vuex'

export default {
  // ...
  methods: {
    ...mapMutations([
      'increment', // mapea `this.increment()` a `this.$store.commit('increment')`

      // `mapMutations`también soporta payloads:
      'incrementBy' // mapea `this.incrementBy(amount)` a `this.$store.commit('incrementBy', amount)`
    ]),
    ...mapMutations({
      add: 'increment' // mapea `this.add()` a `this.$store.commit('increment')`
    })
  }
}
```

### En acciones

Asincronicidad combinado con mutaciones del state puede hacer tu programa muy difícil de entender. Por ejemplo, cuando llamas a dos métodos los dos callbacks asíncronos que hacen una mutación del state. Como sabras cuando serán llamados y cual de ellos sera llamado primero? Esto es exactamente los dos conceptos que queremos separar. En Vuex, **las mutaciones son transacciones síncronas**:

``` js
store.commit('increment')
// cualquier cambio del state que el "increment" pueda causar
// sera terminado en el momento.
```

Para lidiar con operaciones asíncronas, introduciremos [Acciones](actions.md).
