# Instalación

### Descarga Directa / CDN

[https://unpkg.com/vuex](https://unpkg.com/vuex)

<!--email_off-->
[Unpkg.com](https://unpkg.com) provee enlaces CDN basados en NPM. El enlace de arriba siempre apuntara a la ultima version en NPM. Tambien puedes utilizar una version especifica mediante tags con URLs como `https://unpkg.com/vuex@2.0.0`.
<!--/email_off-->

Incluye `vuex` despues de Vue y se instalara el solo automaticamente:

``` html
<script src="/path/to/vue.js"></script>
<script src="/path/to/vuex.js"></script>
```

### NPM

``` bash
npm install vuex --save
```

### Yarn

``` bash
yarn add vuex
```

Cuando se usa un sistema de modulado, puedes explicitamente instalar Vuex mediante `Vue.use()`:

``` js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)
```

No es necesario que hagas esto cuando estes usando script tags globales.

### Promise

Vuex requiere [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises). Si soportas navegadores como (ej IE) no implementes Promise, puedes usar la libreria polyfill, como [es6-promise](https://github.com/stefanpenner/es6-promise).

Puedes incluirlo mediante CDN:

``` html
<script src="https://cdn.jsdelivr.net/npm/es6-promise@4/dist/es6-promise.auto.js"></script>
```

Entonces `window.Promise` estara disponible automaticamente.

Si prefieres utilizar un package manager como NPM o Yarn, instalalo mediante los siguiente comandos:

``` bash
npm install es6-promise --save # NPM
yarn add es6-promise # Yarn
```

Es mas, añade esta linea debajo en cualquier parte de tu codigo antes de usar Vuex:

``` js
import 'es6-promise/auto'
```

### Dev Build

Tendras que clonar directamente desde GitHub y compilar 'vuex' tu mismo si quieres tener la ultima version de desarrollo.

``` bash
git clone https://github.com/vuejs/vuex.git node_modules/vuex
cd node_modules/vuex
npm install
npm run build
```
