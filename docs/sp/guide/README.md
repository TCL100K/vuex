# Comenzando

En el centro de toda aplicación Vuex, es el **store**. Un "store" es básicamente un contenedor que mantiene el **state** de tu aplicación. Hay dos cosas que hacen al store de Vuex diferente a un objecto global plano:

1. Los stores Vuex son reactivos. Cuando un componente Vue recupera el state de ellos, serán re-activamente y eficientemente actualizados, si el store del state cambia.

2. Tu no puedes mutar directamente el store del state. La única manera de cambiar el store del state es explícitamente **committing mutations** (perpetrar mutaciones). Esto asegura que cada cambio del state deje un record de seguimiento viable, y habilita a herramientas a ayudarnos a entender mejor nuestras aplicaciones.

### El store simple

> **NOTA:** usaremos la sintaxis ES2015 para códigos de ejemplo en el resto de la documentación. Si aun no lo has aprendido, [podrías](https://babeljs.io/docs/learn-es2015/)!

Luego [instalando](../installation.md) Vuex, creamos un store. Es bastante sencillo - solo proveemos un objeto state inicial, y algunas mutaciones:

``` js
// Asegúrate primero de llamar a Vue.use(Vuex) si estas usando un sistema de modulado

const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  }
})
```

Ahora, puedes acceder al objeto state como `store.state`, y desencadenar un cambio con el método `store.commit`:

``` js
store.commit('increment')

console.log(store.state.count) // -> 1
```

De nuevo, la razón por la que commiteamos la mutación en vez de aumentar `store.state.count` directamente, es porque queremos explícitamente poder seguir el cambio. Esta simple convención hace tu intención mas explicita, entonces puedes razonar sobre los cambios en el state de tu app mejor, cuando lees el código. Ademas, nos da la oportunidad de implementar herramientas que puedan loggear cada mutación, tomar snapshots del state, incluso hacer viajes en el tiempo en la depuración.

Usando el store state en un componente simplemente involucra en retornar el state en una propiedad computada (computed), porque el store state es reactivo. Desencadenar un cambio simplemente significa commitear una mutación en los métodos de los componentes.

Aquí un ejemplo de [la mas básica app de conteo en Vuex](https://jsfiddle.net/n9jmu5v7/1269/).

En el siguiente, vamos a discutir cada concepto en mucho mas detalle, empezado por el [State](state.md).
