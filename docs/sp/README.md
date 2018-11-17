# Qué es Vuex?

<a id="vuex-video-preview" href="javascript:var vuexVideoPreviewEl = document.getElementById('vuex-video-preview'); var videoWrapperEl = document.createElement('div'); videoWrapperEl.innerHTML = '<iframe src=&quot;https://player.vimeo.com/video/297515936?autoplay=1&quot; width=&quot;640&quot; height=&quot;360&quot; frameborder=&quot;0&quot; webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>'; vuexVideoPreviewEl.parentNode.insertBefore(videoWrapperEl, vuexVideoPreviewEl); vuexVideoPreviewEl.parentNode.removeChild(vuexVideoPreviewEl)"><img src="/vuex-explained-visually.png" alt="Play Vuex Explained Visually Video" style="border-radius: 6px;"></a>

Vuex es un **patron de manejo de states + libreria** para aplicaciones Vue.js. Sirve como un almacenamiento centralizado para todos los componentes de una aplicacion, con reglas que aseguren que el state puede ser solo mutado por una accion predecible. Tambien se integra con la extension oficial Vue's [devtools extension](https://github.com/vuejs/vue-devtools) para proveer herramientas avanzadas como depuracion zero-config time-travel y importacion / exportacion de snapshot de state.

### Que es un "Patron de manejo de state"?

Empezemos con una simple aplicacion Vue de conteo:

``` js
new Vue({
  // state
  data () {
    return {
      count: 0
    }
  },
  // view
  template: `
    <div>{{ count }}</div>
  `,
  // actions
  methods: {
    increment () {
      this.count++
    }
  }
})
```

Es una aplicacion independiente con las siguientes partes:

- El **state**, que es una fuente de verdad que maneja nuestra aplicacion;
- El **view**, que es solo un mapeo declarativo de el **state**;
- Las **actions**, que son las posibles vias que el state puede cambiar en reaccion entrada que puede ingresar el usario en la **view**.

Esta es una extremadamente simple representacion de el concepto de "el sentiodo unico que los datos fluyen":

<p style="text-align: center; margin: 2em">
  <img style="width:100%;max-width:450px;" src="/flow.png">
</p>

Sin embargo, la simplicida rapidamente se desmorona cuando tenemos **multiples componentes que comparten el mismo state**:

- Multiples views podrian depender del mismo pedazo de state.
- Actions de diferentes views mutaran la misma parte del state.

Para el primer problema, pasar props puede ser tediosio para componentes profundamente anidados, y simplemente no funciona para componentes hermanos. Para el problema dos, a menudo nos encontramos en soluciones como conectar directamente instancias padre/hijo o tratando de mutar y sincronizar multiples copias del state mediante eventos. Ambos patrones son propensos a convertirse en codigo inmantenible.

Entonces porque no estramos el codigo compartido del state fuera de los componentes, y lo manejamos como un singleton global? Con esto, nuestro arbol de comoponentes se convierte en una gran "view", y cualquier componente puede acceder al state o desencadenar acciones, no importa en que parte esten del arbol.

Agregando, definiendo y ceparando conceptos en el manejo del state y asegurando ciertas reglas, tambien le damos a nuestro codigo mas estructura y mantenibilidad.

Esta es la idea basica detras de Vuex, inspirada por [Flux](https://facebook.github.io/flux/docs/overview.html), y [The Elm Architecture](https://guide.elm-lang.org/architecture/). A diferencias de otros patrones, Vuex es tambien una libreria personalizada para Vue.js para tomar ventaja de su sistema de reactividad granular para actualizaciones eficientes.

![vuex](/vuex.png)

### Cuando deberia usarlo?

A pesar de que Vuex nos ayuda a lidiar con compartir el manejo del state, tambien viene con mas conceptos y estandares. Es un intercambio entre produccion de termino corto y largo.

Si nunca construiste un SPA a larga escala y saltas a Vuex, puede sentirse verboso y abrumador. Es completamente normal - si tu app es simple, podrias estar mejor sin Vuex. Un simple [store pattern](https://vuejs.org/v2/guide/state-management.html#Simple-State-Management-from-Scratch) puede ser todo lo que necesites. Pero si tu estas construyendo a SPA de madia a gran escala, hay mas chances que entres en situaciones que ten hagan pensar como podria manejar esto de una mejor manera fuera de tus componentes Vue, y Vuex podria ser el siguente paso natural para ti. Esta es una gran cita de Dam Abramov, el autor de Redux:

> Las librerias Flux son como vidrio: sabes cuando las necesitas.
