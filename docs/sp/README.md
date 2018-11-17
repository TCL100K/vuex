# Qué es Vuex?

<a id="vuex-video-preview" href="javascript:var vuexVideoPreviewEl = document.getElementById('vuex-video-preview'); var videoWrapperEl = document.createElement('div'); videoWrapperEl.innerHTML = '<iframe src=&quot;https://player.vimeo.com/video/297515936?autoplay=1&quot; width=&quot;640&quot; height=&quot;360&quot; frameborder=&quot;0&quot; webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>'; vuexVideoPreviewEl.parentNode.insertBefore(videoWrapperEl, vuexVideoPreviewEl); vuexVideoPreviewEl.parentNode.removeChild(vuexVideoPreviewEl)"><img src="/vuex-explained-visually.png" alt="Play Vuex Explained Visually Video" style="border-radius: 6px;"></a>

Vuex es un **patrón de manejo de states + librería** para aplicaciones Vue.js. Sirve como un almacenamiento centralizado para todos los componentes de una aplicación, con reglas que aseguren que el state puede ser solo mutado por una acción predecible. También se integra con la extension oficial Vue's [devtools extensión](https://github.com/vuejs/vue-devtools) para proveer herramientas avanzadas como depuración zero-config time-travel y importación / exportación de snapshot del state.

### Que es un "Patrón de manejo de states"?

Empecemos con una simple aplicación Vue de conteo:

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

Es una aplicación independiente con las siguientes partes:

- El **state**, que es una fuente de verdad que maneja nuestra aplicación;
- El **view**, que es solo un mapeo declarativo del **state**;
- Las **actions**, que son las posibles vías que el state puede cambiar en reacción a la entrada que puede ingresar el usuario en el **view**.

Esta es una extremadamente simple representación del concepto del "sentido único en que los datos fluyen":

<p style="text-align: center; margin: 2em">
  <img style="width:100%;max-width:450px;" src="/flow.png">
</p>

Sin embargo, la simplicidad rápidamente se desmorona cuando tenemos **múltiples componentes que comparten el mismo state**:

- Múltiples views podrían depender del mismo pedazo de state.
- Actions de diferentes views mutaran la misma parte del state.

Para el primer problema, pasar props puede ser tedioso para componentes profundamente anidados, y simplemente no funciona para componentes hermanos. Para el problema dos, a menudo nos encontramos en soluciones como conectar directamente instancias padre/hijo o tratando de mutar y sincronizar múltiples copias del state mediante eventos. Ambos patrones son propensos a convertirse en código inmantenible.

Entonces porque no extraemos el código compartido del state fuera de los componentes, y lo manejamos como un singleton global? Con esto, nuestro árbol de componentes se convierte en una gran "view", y cualquier componente puede acceder al state o desencadenar acciones, no importa en que parte del árbol estén.

Agregando, definiendo y separando conceptos en el manejo del state y asegurando ciertas reglas, también le damos a nuestro código mas estructura y mantenibilidad.

Esta es la idea básica detrás de Vuex, inspirada por [Flux](https://facebook.github.io/flux/docs/overview.html), y [La arquitectura Elm](https://guide.elm-lang.org/architecture/). A diferencias de otros patrones, Vuex es también una librería personalizada para Vue.js para tomar ventaja de su sistema de re-actividad granular para actualizaciones eficientes.

![vuex](/vuex.png)

### Cuando debería usarlo?

A pesar de que Vuex nos ayuda a lidiar en compartir el manejo del state, también viene con mas conceptos y estándares. Es un intercambio entre producción de termino corto y largo plazo.

Si nunca construiste un SPA a larga escala y saltas a Vuex, puede sentirse verboso y abrumador. Es completamente normal - si tu app es simple, podrías estar mejor sin Vuex. Un simple [patrón de almacenado](https://vuejs.org/v2/guide/state-management.html#Simple-State-Management-from-Scratch) puede ser todo lo que necesites. Pero si tu estas construyendo un SPA de media a gran escala, hay mas chances que entres en situaciones que te hagan pensar como podría manejar esto de una mejor manera fuera de tus componentes Vue, y Vuex podría ser el siguiente paso natural para ti. Esta es una gran cita de Dam Abramov, el autor de Redux:

> Las librerías Flux son como anteojos: sabes cuando los necesitas.
