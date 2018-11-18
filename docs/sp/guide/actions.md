# Acciones

Las acciones o _actions_ son similares a las mutaciones, con estas diferencias:

- En vez de mutar el state, las acciones commitean mutaciones.
- Las acciones pueden contener operaciones arbitrarias asíncronas.

Registremos una simple acción:

``` js
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
  }
})
```

Los handlers de las acciones reciben un objecto context que expone el mismos métodos/propiedades que en la instancia del store, entonces puedes llamar `context.commit` para commitear una mutación, o acceder al state y getters via `context.state` y `context.getters`. Incluso podemos llamar otras acciones con `context.dispatch`. Ahora veremos por que este objecto context no es la instancia del store en si, cuando introduzcamos [Modulos](modules.md) próximamente.

En la practica, a menudo usamos ES2015 [deconstrucción de argumentos](https://github.com/lukehoban/es6features#destructuring) para simplificar el código un poco (especialmente cuando necesitamos llamar a `commit` varias veces):

``` js
actions: {
  increment ({ commit }) {
    commit('increment')
  }
}
```

### Despachando acciones

Las acciones son desencadenadas por el método `store.dispatch`:

``` js
store.dispatch('increment')
```

Esto se podría ver un poco tonto a primera vista: si queremos incrementar el contador, por que no simplemente llamamos a `store.commit('increment')` directamente? Recuerdas que **las mutaciones tienen que ser síncronas**? Las acciones no. Podemos realizar operaciones asíncronas dentro de una acción:

``` js
actions: {
  incrementAsync ({ commit }) {
    setTimeout(() => {
      commit('increment')
    }, 1000)
  }
}
```

Las acciones soportan el mismo formato de payload y estilo de objeto cuando realizamos el despacho:

``` js
// despacho con un payload
store.dispatch('incrementAsync', {
  amount: 10
})

// despacho con un objeto
store.dispatch({
  type: 'incrementAsync',
  amount: 10
})
```

Un ejemplo mas practico de acciones del mundo real puede ser el checkear un carrito de compras, que involucra **llamar a una API asíncrona** y **commitear multiples mutaciones**:

``` js
actions: {
  checkout ({ commit, state }, products) {
    // guarda los items actuales en el carrito
    const savedCartItems = [...state.cart.added]
    // enviamos pedido de checkeo, y con optimismo
    // limpiamos el carrito
    commit(types.CHECKOUT_REQUEST)
    // el API de la tienda acepta un callback exitoso y un callback fallido
    shop.buyProducts(
      products,
      // handle del exitoso
      () => commit(types.CHECKOUT_SUCCESS),
      // handle del fallido
      () => commit(types.CHECKOUT_FAILURE, savedCartItems)
    )
  }
}
```

Note que realizamos un flujo de operaciones asíncronas, y guardando los efectos de lado (mutaciones de state) de la acción commiteandolas.

### Despachando acciones en componentes

Puedes despachar acciones en componentes con `this.$store.dispatch('xxx')`, o usar el helper `mapActions` que mapea los métodos del componente llamando a `store.dispatch` (require que el `store` este inyectado en la instancia principal):

``` js
import { mapActions } from 'vuex'

export default {
  // ...
  methods: {
    ...mapActions([
      'increment', // mapea `this.increment()` a `this.$store.dispatch('increment')`

      // `mapActions` tambien soporta payloads:
      'incrementBy' // mapea `this.incrementBy(amount)` a `this.$store.dispatch('incrementBy', amount)`
    ]),
    ...mapActions({
      add: 'increment' // mapea `this.add()` a `this.$store.dispatch('increment')`
    })
  }
}
```

### Componiendo Acciones

Las acciones comúnmente son asíncronas, entonces como podemos saber cuando una acción haya terminado? Y mas importante, como podemos componer multiples acciones juntas para lidiar con flujo asíncrono mas complejo?

La primero que sabemos es que `store.dispatch` puede lidiar con un Promise retornado por el handler de la acción desencadenada y también retorna un Promise:

``` js
actions: {
  actionA ({ commit }) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        commit('someMutation')
        resolve()
      }, 1000)
    })
  }
}
```

Ahora podemos hacer:

``` js
store.dispatch('actionA').then(() => {
  // ...
})
```

Y también en otra acción:

``` js
actions: {
  // ...
  actionB ({ dispatch, commit }) {
    return dispatch('actionA').then(() => {
      commit('someOtherMutation')
    })
  }
}
```

Finalmente, si hacemos uso de [async / await](https://tc39.github.io/ecmascript-asyncawait/), podemos componer nuestras acciones así:

``` js
// asumiendo que `getData()` y `getOtherData()` retornan Promises

actions: {
  async actionA ({ commit }) {
    commit('gotData', await getData())
  },
  async actionB ({ dispatch, commit }) {
    await dispatch('actionA') // espera por un `actionA` para terminar
    commit('gotOtherData', await getOtherData())
  }
}
```

> Es posible con `store.dispatch` desencadenar multiples handlers de acciones en diferentes módulos. En caso de que el valor retornado sean un Promise eso resuelve cuando todos los handlers desencadenados se resolverán.
