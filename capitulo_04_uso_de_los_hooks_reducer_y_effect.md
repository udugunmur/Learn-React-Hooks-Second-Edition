# Parte 2: Uso de Hooks con ejemplos del mundo real

## Capítulo 4: Uso de los Hooks Reducer y Effect

Tras desarrollar nuestra propia aplicación de blog utilizando el Hook de Estado, ahora vamos a aprender sobre otros dos Hooks muy importantes proporcionados por React: los **Hooks Reducer y Effect**. Primero aprenderemos cuándo debemos usar un Hook Reducer en lugar de un Hook de Estado. Luego, aprenderemos a transformar nuestro Hook de Estado existente en un Hook Reducer para asimilar el concepto en la práctica. Finalmente, aprenderemos sobre los Hooks de Efecto, para qué se utilizan y cómo implementarlos en nuestra aplicación de blog.

En este capítulo, cubriremos los siguientes temas principales:

- Hooks Reducer frente a Hooks de Estado
- Uso de Hooks Reducer
- Uso de Hooks de Efecto

---

### Requisitos técnicos

Debe estar instalada una versión bastante reciente de **Node.js**. El gestor de paquetes de Node (**npm**) también debe estar instalado (debería venir incluido con Node.js). Para obtener más información sobre cómo instalar Node.js, consulta su sitio web oficial: [https://nodejs.org/](https://nodejs.org/).

Utilizaremos **Visual Studio Code (VS Code)** para las guías de este libro, pero todo debería funcionar de manera similar en cualquier otro editor. Para obtener más información sobre cómo instalar VS Code, consulta su sitio web oficial: [https://code.visualstudio.com](https://code.visualstudio.com/).

En este libro, utilizamos las siguientes versiones:

- **Node.js** v22.14.0
- **npm** v10.9.2
- **Visual Studio Code** v1.97.2

Aunque instalar una versión más reciente no debería ser un problema, ten en cuenta que ciertos pasos podrían funcionar de manera diferente en una versión más nueva. Si tienes algún problema con el código y los pasos proporcionados en este libro, intenta utilizar las versiones mencionadas.

Puedes encontrar el código de este capítulo en GitHub: [https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter04](https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter04).

Se recomienda encarecidamente que escribas el código por tu cuenta. No te limites a ejecutar simplemente los ejemplos de código proporcionados en el libro. Es importante escribir el código tú mismo para poder aprenderlo y comprenderlo adecuadamente. Sin embargo, si encuentras algún problema, siempre puedes consultar los ejemplos de código.

---

### Hooks Reducer frente a Hooks de Estado

En el capítulo anterior, aprendimos a manejar estados locales y globales. Utilizamos Hooks de Estado para ambos casos, lo cual es adecuado para cambios de estado simples. Sin embargo, si nuestra lógica de estado se vuelve más compleja, necesitaremos asegurarnos de mantener el estado consistente. Para lograrlo, deberíamos usar un **Hook Reducer** en lugar de múltiples Hooks de Estado, ya que es más difícil mantener la sincronía entre varios Hooks de Estado que dependen unos de otros. Como alternativa, podríamos mantener todos los estados en un único Hook de Estado, pero tendríamos que asegurarnos de no sobrescribir accidentalmente partes de nuestro estado.

#### Limitaciones del Hook de Estado (*Limitations of the State Hook*)

El Hook de Estado ya admite el paso de objetos y arrays complejos, y puede gestionar sus cambios de estado sin problemas. Sin embargo, siempre tendremos que modificar el estado directamente, lo que significa que necesitamos usar mucha desestructuración para asegurarnos de no sobrescribir otras partes del estado. Por ejemplo, imagina que tenemos un Hook de Estado como el siguiente:

```javascript
const [config, setConfig] = useState({
  filter: 'all',
  expandPosts: true,
})
```

Ahora, supongamos que queremos cambiar el filtro de la siguiente manera:

```javascript
setConfig({
  filter: {
    author: 'Daniel Bugl',
    fromDate: '2024-10-02',
  },
})
```

Si simplemente hiciéramos esto, ¡estaríamos eliminando la configuración `expandPosts` de nuestro objeto de estado! Por lo tanto, necesitamos usar el operador de propagación (*spread operator*), así:

```javascript
setConfig(config => ({
  ...config,
  filter: {
    author: 'Daniel Bugl',
    fromDate: '2024-10-02',
  }
}))
```

Ahora, si quisiéramos cambiar el filtro `fromDate` a una fecha diferente, necesitaríamos usar el operador de propagación dos veces, para evitar eliminar accidentalmente el filtro de autor:

```javascript
setConfig(config => ({
  ...config,
  filter: {
    ...config.filter,
    fromDate: '2024-10-03',
  }
}))
```

¿Pero qué sucede si hacemos esto cuando el estado del filtro sigue siendo una cadena de texto, como estaba en el objeto original (`filter: 'all'`)? Obtendremos el siguiente resultado:

```javascript
{
  filter: {
    '0': 'a',
    '1': 'l',
    '2': 'l',
    fromDate: '2024-10-03',
  },
  expandPosts: true
}
```

¿Por qué aparecen repentinamente tres nuevas claves: `'0'`, `'1'` y `'2'`? La respuesta es que el operador de propagación también funciona en cadenas de texto, las cuales se propagan asignando a cada letra una clave basada en su índice dentro de la cadena.

Como puedes imaginar, usar el operador de propagación y cambiar el objeto de estado directamente puede volverse muy tedioso para objetos de estado más grandes. Además, siempre debemos asegurarnos de no introducir errores, y tendríamos que buscar fallos en múltiples lugares de nuestra aplicación.

#### Reducers

En lugar de modificar el estado directamente, podríamos crear una función que se encargue de los cambios de estado. Dicha función se llama **reducer** y funciona de la siguiente manera:

```javascript
const newState = reducer(currentState, action)
```

Como puedes ver, en lugar de modificar directamente el objeto de estado, llamamos a una función que recibe el estado actual y un objeto de acción, y devuelve un nuevo objeto de estado. Antes de definir la función, echemos un vistazo más de cerca a las acciones.

#### Acciones (*Actions*)

Las acciones son objetos que tienen una propiedad `type` que contiene el nombre de la acción y, opcionalmente, información adicional sobre la misma.

Revisemos nuestro objeto de estado anterior:

```javascript
{
  filter: 'all',
  expandPosts: true,
}
```

Si quisiéramos cambiar el estado `expandPosts`, usaríamos una acción `TOGGLE_EXPAND`, la cual no necesita información adicional. La acción se vería así:

```javascript
{ type: 'TOGGLE_EXPAND' }
```

Si quisiéramos cambiar el filtro en su lugar, usaríamos una acción `CHANGE_FILTER`, la cual contiene adicionalmente información sobre el filtro que debe cambiarse. Por ejemplo, podríamos usar las siguientes acciones para cambiar el filtro de diferentes maneras:

```javascript
{ type: 'CHANGE_FILTER', all: true }
{ type: 'CHANGE_FILTER', fromDate: '2024-10-02' }
{ type: 'CHANGE_FILTER', author: 'Daniel' }
{ type: 'CHANGE_FILTER', fromDate: '2024-10-03' }
```

La segunda, tercera y cuarta acción cambiarían el estado del filtro de una cadena de texto a un objeto y luego establecerían la clave respectiva. Si el objeto ya existe, simplemente ajustaría las claves que se definieron en la acción. Después de cada una de estas acciones, el estado cambiaría de la siguiente manera:

```javascript
{ filter: 'all' }
{ filter: { fromDate: '2024-10-02' } }
{ filter: { fromDate: '2024-10-02', author: 'Daniel' } }
{ filter: { fromDate: '2024-10-03', author: 'Daniel' } }
```

Ahora imaginemos que aplicamos la siguiente acción:

```javascript
{ type: 'CHANGE_FILTER', all: true }
```

Después de esta acción, el filtro volvería a la cadena `'all'`, tal como estaba en su estado inicial.

> Si has trabajado antes con la biblioteca Redux, ya estarás familiarizado con los conceptos de estado, acciones y reducers.

#### Definición de reducers (*Defining reducers*)

Una función reducer para las acciones que definimos podría ser la siguiente:

```javascript
function reducer(state, action) {
  switch (action.type) {
```

Aquí definimos la función y decidimos qué hacer con el estado en función de `action.type`. Primero, manejamos la acción `TOGGLE_EXPAND`:

```javascript
    case 'TOGGLE_EXPAND':
      return { ...state, expandPosts: !state.expandPosts }
```

Ahora, se maneja la acción `CHANGE_FILTER`, donde restablecemos el filtro a la cadena `'all'` si la acción definió el filtro `all: true`:

```javascript
    case 'CHANGE_FILTER':
      if (action.all) {
        return { ...state, filter: 'all' }
      }
```

Si el filtro sigue siendo una cadena, inicializamos un objeto vacío; de lo contrario, reutilizamos el objeto existente:

```javascript
      let filter = typeof state.filter === 'string' ? {} : state.filter
```

Ahora podemos establecer los filtros `fromDate` y `author`, según cuáles se hayan definido en la acción:

```javascript
      if (action.fromDate) {
        filter.fromDate = action.fromDate
      }
      if (action.author) {
        filter.author = action.author
      }
```

Finalmente, se devuelve el estado con el nuevo filtro:

```javascript
      return { ...state, filter }
```

En caso de que un tipo de acción sea desconocido, lanzamos un error:

```javascript
    default:
      throw new Error('unknown action')
  }
}
```

> Lanzar un error en el caso `default` es diferente de lo que haríamos con los reducers de Redux, donde simplemente devolveríamos el estado actual. Los Hooks Reducer de React no almacenan todos los estados en un único objeto global y solo vamos a manejar ciertas acciones para ciertos objetos de estado, por lo que podemos lanzar un error ante tipos de acción desconocidos.

Si bien todavía estamos usando algunos operadores de propagación en la función reducer, el código no está tan profundamente anidado. Además, todo el manejo del estado se encuentra en un solo lugar y solo modificamos una parte del estado a la vez mediante acciones, lo que hace que el código sea mucho más fácil de mantener y menos propenso a errores.

#### El Hook Reducer (*The Reducer Hook*)

Ahora que tenemos una función reducer, solo necesitamos definir un estado inicial:

```javascript
const initialState = { filter: 'all' }
```

Con la función reducer y el estado inicial, podemos crear un Hook Reducer:

```javascript
const [state, dispatch] = useReducer(reducer, initialState)
```

Ahora se puede acceder al estado actual mediante el objeto `state` devuelto por el Hook. El uso de la función `dispatch` nos permite invocar la función reducer que pasamos al Hook Reducer. Las acciones se pueden despachar mediante la función `dispatch`. Por ejemplo:

```javascript
dispatch({ type: 'TOGGLE_EXPAND' })
```

Despachar una acción llamará a la función reducer con el estado actual y la acción despachada, y establecerá el estado devuelto como el nuevo estado del Hook Reducer.

Si queremos agregar información adicional a la acción, simplemente la añadimos al objeto:

```javascript
dispatch({ type: 'CHANGE_FILTER', fromDate: '2024-10-03' })
```

Como podemos ver, gestionar los cambios de estado mediante acciones y reducers es mucho más fácil de leer y mantener que tener que ajustar el objeto de estado directamente.

Ahora que hemos aprendido sobre los Hooks Reducer y cuándo usarlos en lugar de los Hooks de Estado, comencemos a utilizarlos.

---

### Uso de Hooks Reducer

Cualquier Hook de Estado existente se puede convertir en un Hook Reducer cuando el estado o los cambios de estado se vuelven demasiado complejos.

> Si hay múltiples funciones `setState` que siempre se llaman al mismo tiempo, es un buen indicio de que deberían agruparse en un único Hook Reducer.

El estado global suele ser un buen candidato para usar un Hook Reducer en lugar de un Hook de Estado, ya que los cambios pueden ocurrir en cualquier parte de la aplicación. Resulta mucho más fácil gestionar los cambios de estado cuando solo se procesan en una función y los componentes despachan acciones en lugar de modificar directamente el estado. Tener toda la lógica de cambio de estado en un solo lugar facilita el mantenimiento y la resolución de errores, sin introducir nuevos fallos al olvidar actualizar la lógica en todas partes.

#### Convertir un Hook de Estado en un Hook Reducer

En nuestra aplicación de blog, tenemos dos Hooks de Estado globales:

- El estado `username`: contiene el nombre de usuario del usuario actualmente autenticado.
- El estado `posts`: contiene todas las publicaciones de nuestro feed.

El estado `username` es bastante simple: solo contiene una cadena de texto con el nombre de usuario. Por lo tanto, en este momento no tiene sentido convertirlo en un Hook Reducer, ya que los cambios de estado son directos:
- Al iniciar sesión/registrarse: Establecer el nombre de usuario.
- Al cerrar sesión: Limpiar el nombre de usuario.

Para el estado `posts`, sin embargo, ya tuvimos que usar el operador de propagación para evitar eliminar accidentalmente publicaciones del feed al crear una nueva publicación. Por lo tanto, parece un buen candidato para un Hook Reducer, especialmente considerando que podría ampliarse en el futuro (obtener nuevas publicaciones, actualizarlas, eliminarlas, etc.).

Comencemos reemplazando el Hook de Estado `posts` por un Hook Reducer.

#### Definición de acciones (*Defining actions*)

Comenzamos definiendo las acciones para nuestro Hook Reducer. Por ahora, solo consideraremos una acción `CREATE_POST`:

```javascript
{
  type: 'CREATE_POST',
  post: {
    title: 'React Hooks',
    content: 'The greatest thing since sliced bread!',
    author: 'Daniel Bugl',
  },
}
```

A continuación, implementaremos la función reducer.

#### Implementación del reducer (*Implementing the reducer*)

Por ahora, colocaremos nuestro reducer en un archivo `src/reducers.js`. Más adelante, si tenemos muchos reducers, podría tener sentido crear una carpeta `src/reducers/` separada, con archivos independientes para cada función reducer.

Comencemos a implementar la función reducer:

1. Copia la carpeta `Chapter03_2` a una nueva carpeta `Chapter04_1` ejecutando el siguiente comando:

```bash
cp -R Chapter03_2 Chapter04_1
```

2. Abre la nueva carpeta `Chapter04_1` en VS Code.
3. Crea un nuevo archivo `src/reducers.js`, en el cual definimos y exportamos la función `postsReducer`:

```javascript
export function postsReducer(state, action) {
```

4. Usamos una declaración `switch` para manejar los diferentes tipos de acción:

```javascript
  switch (action.type) {
```

5. A continuación, manejamos la acción `CREATE_POST`, insertando la nueva publicación (proveniente de `action.post`) al principio del array:

```javascript
    case 'CREATE_POST':
      return [action.post, ...state]
```

6. Por ahora, este será el único tipo de acción que manejemos, por lo que podemos definir la declaración `default` ahora, lanzando un error cuando encontremos un tipo de acción desconocido:

```javascript
    default:
      throw new Error('Unknown action type')
  }
}
```

Tras definir la función reducer, ahora podemos usarla para definir un Hook Reducer.

#### Definición del Hook Reducer (*Defining the Reducer Hook*)

Para definir un Hook Reducer, sigue estos pasos:

1. Edita `src/App.jsx` e importa las funciones `useReducer` y `postsReducer`:

```javascript
import { useState, useReducer } from 'react'
import { postsReducer } from './reducers.js'
```

2. Elimina el siguiente Hook de Estado:

```javascript
export function App() {
  const [posts, setPosts] = useState(defaultPosts)
```

Reemplázalo por un Hook Reducer:

```javascript
export function App() {
  const [posts, dispatch] = useReducer(postsReducer, defaultPosts)
```

3. Luego, en lugar de `setPosts`, pasa la función `dispatch` al componente `CreatePost` y elimina la prop `setPosts`:

```jsx
      {username && <CreatePost username={username} dispatch={dispatch} />}
```

4. A continuación, edita `src/post/CreatePost.jsx` y reemplaza la prop `setPosts` con la prop `dispatch`:

```javascript
export function CreatePost({ username, dispatch }) {
```

5. En lugar de agregar imperativamente la nueva publicación, despachamos una acción dentro de la función `handleSubmit`:

```javascript
  function handleSubmit(e) {
    e.preventDefault()
    const form = e.target
    const title = form.elements.title.value
    const content = form.elements.content.value
    const newPost = { title, content, author: username }
    dispatch({ type: 'CREATE_POST', post: newPost })
    form.reset()
  }
```

> Para acciones más complejas, podría tener sentido definir funciones que creen el objeto de acción, los llamados **creadores de acciones (*action creators*)**. Por ejemplo, una función `createPostAction(post)` podría crear y devolver el objeto de acción `CREATE_POST`. Los creadores de acciones pueden ayudar a garantizar una estructura coherente de los objetos de acción, facilitar su creación y permitir que esta estructura se ajuste fácilmente en el futuro.

Ahora el estado `posts` utiliza un Hook Reducer en lugar de un Hook de Estado, ¡pero funciona de la misma manera que antes! Si queremos agregar más lógica para administrar publicaciones más adelante (como eliminarlas y editarlas), será mucho más fácil hacerlo ahora.

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter04/Chapter04_1`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

Tras conocer el Hook Reducer, continuemos aprendiendo sobre el Hook de Efecto.

---

### Uso de Hooks de Efecto

El **Hook de Efecto (*Effect Hook*)** es un Hook importante para sincronizar tus componentes con sistemas externos, como APIs externas o las APIs del navegador. Sin embargo, a menudo se usa en exceso en el código de React. Si no hay ningún sistema externo involucrado, no deberías usar un Hook de Efecto.

En el caso de nuestro blog, implementaremos una forma de verificar si el usuario tiene un rol de administrador en el componente `Logout`. Por simplicidad y para centrarnos en el Hook de Efecto en sí, simplemente simularemos esta verificación, pero imagina que esto lo realiza una API externa.

#### ¿Recuerdas componentDidMount y componentDidUpdate?

Si has trabajado antes con versiones anteriores de React, probablemente hayas utilizado los métodos de ciclo de vida `componentDidMount` y `componentDidUpdate`. Por ejemplo, si quisiéramos establecer el título de una página web a partir de una prop dada usando componentes de clase de React, necesitaríamos agregar el siguiente método de ciclo de vida:

```javascript
import React from 'react'

class App extends React.Component {
  componentDidMount() {
    const { title } = this.props
    document.title = title
  }

  render() {
    return <div>Example App</div>
  }
}
```

Esto funciona bien. Sin embargo, cuando la prop `title` se actualiza, el cambio no se refleja en el título de nuestra página web. Para resolver este problema, necesitamos definir el método de ciclo de vida `componentDidUpdate`, de la siguiente manera:

```javascript
import React from 'react'

class App extends React.Component {
  componentDidMount() {
    const { title } = this.props
    document.title = title
  }

  componentDidUpdate(prevProps) {
    const { title } = this.props
    if (title !== prevProps.title) {
      document.title = title
    }
  }

  render() {
    return <div>Example App</div>
  }
}
```

Habrás notado que estamos haciendo exactamente lo mismo dos veces; por lo tanto, podríamos crear un nuevo método para gestionar las actualizaciones del título y luego llamarlo desde ambos métodos de ciclo de vida:

```javascript
import React from 'react'

class App extends React.Component {
  updateTitle() {
    const { title } = this.props
    document.title = title
  }

  componentDidMount() {
    this.updateTitle()
  }

  componentDidUpdate(prevProps) {
    if (this.props.title !== prevProps.title) {
      this.updateTitle()
    }
  }

  render() {
    return <div>Example App</div>
  }
}
```

Sin embargo, todavía necesitamos llamar a `this.updateTitle()` dos veces. Cuando actualicemos el código más adelante y, por ejemplo, pasemos un argumento a `this.updateTitle()`, siempre debemos recordar pasarlo a ambas llamadas de la función. Si olvidamos actualizar uno de los métodos de ciclo de vida, podríamos introducir errores. Además, debemos agregar una condición `if` a `componentDidUpdate` para evitar llamar a `this.updateTitle()` cuando la prop `title` no haya cambiado.

#### De métodos de ciclo de vida a Hooks de Efecto

En el mundo de los Hooks, los métodos de ciclo de vida `componentDidMount` y `componentDidUpdate` se combinan en el Hook `useEffect`, el cual (cuando no se especifica un array de dependencias) se ejecuta en cada rerenderización.

Por lo tanto, en lugar de usar un componente de clase, ahora podemos definir un componente de función con un Hook de Efecto, que haría lo mismo que antes:

```javascript
import { useEffect } from 'react'

function App({ title }) {
  useEffect(() => {
    document.title = title
  })

  return <div>Example App</div>
}
```

¡Y eso es todo lo que necesitamos hacer! El Hook de Efecto llamará a la función proporcionada cada vez que el componente se vuelva a renderizar.

> Desde React 19, es posible cambiar el título (o cualquier etiqueta de metadatos) de una página web definiendo un elemento `<title>` (o `<link>` o `<meta>`) en cualquier componente. Estos elementos se elevarán automáticamente (*hoisted*) a la sección `<head>`.

#### Disparar un efecto solo cuando cambian ciertas props

Si queremos asegurarnos de que nuestra función de efecto solo se llame cuando cambie la prop `title`, podemos especificar qué valores deben desencadenar los cambios como un segundo argumento para el Hook `useEffect`: el **array de dependencias (*dependency array*)**:

```javascript
useEffect(() => {
  document.title = title
}, [title])
```

El array de dependencias no se limita únicamente a las props; podemos usar cualquier valor disponible dentro del cuerpo del componente, incluso variables definidas dentro del componente y valores de otros Hooks (como un Hook de Estado o un Hook Reducer):

```javascript
const [title, setTitle] = useState('')

useEffect(() => {
  document.title = title
}, [title])
```

Como podemos ver, usar un Hook de Efecto es mucho más sencillo que lidiar con métodos de ciclo de vida. Todo lo que necesitamos especificar es de qué valores debe depender el Hook de Efecto. Cada vez que cualquiera de estos valores cambia, la función de efecto se vuelve a ejecutar automáticamente.

#### Disparar un efecto solo al montar (*Triggering an effect only on mount*)

Si queremos replicar el comportamiento de solo agregar un Hook de ciclo de vida `componentDidMount`, sin activarlo cuando cambien las props, podemos hacerlo pasando un array vacío como segundo argumento al Hook `useEffect`:

```javascript
useEffect(() => {
  document.title = title
}, [])
```

Pasar un array vacío significa que nuestra función de efecto solo se activará una vez cuando el componente se monte, y no se activará cuando cambien las props. Sin embargo, en lugar de pensar en el montaje de los componentes, con los Hooks debemos pensar en las **dependencias de los efectos**. En este caso, el efecto no tiene dependencias, lo que significa que solo se ejecutará una vez. Si un efecto tiene dependencias especificadas, se ejecutará nuevamente cuando cambie cualquiera de las dependencias.

#### Limpieza de efectos (*Cleaning up effects*)

A veces los efectos deben limpiarse cuando el componente se desmonta. Para hacerlo, podemos devolver una función desde el Hook de Efecto. Esta función devuelta funciona de manera similar al método de ciclo de vida `componentWillUnmount`:

```javascript
useEffect(() => {
  const updateInterval = setInterval(
    () => console.log('fetching update'),
    updateTime,
  )
  return () => clearInterval(updateInterval)
}, [updateTime])
```

El código devuelto se denomina **función de limpieza (*cleanup function*)**. La función de limpieza se llamará cuando el componente se desmonte y antes de volver a ejecutar el efecto. Esto evita errores cuando, por ejemplo, cambia la prop `updateTime`. En ese caso, el efecto anterior se limpiará y se definirá un intervalo con el nuevo `updateTime`.

#### Implementación de un Hook de Efecto en nuestra aplicación de blog

Ahora que hemos aprendido cómo funciona el Hook de Efecto, vamos a usarlo en nuestra aplicación de blog para implementar una forma de verificar el rol del usuario cuando ya ha iniciado sesión.

Implementemos un Hook de Efecto siguiendo estos pasos:

1. Copia la carpeta `Chapter04_1` a una nueva carpeta `Chapter04_2` ejecutando el siguiente comando:

```bash
cp -R Chapter04_1 Chapter04_2
```

2. Abre la nueva carpeta `Chapter04_2` en VS Code.
3. Edita `src/user/Logout.jsx` e importa las funciones `useState` y `useEffect`:

```javascript
import { useState, useEffect } from 'react'
```

4. Luego, define una función que simule una API externa encargada de verificar el rol de un usuario:

```javascript
function getRole(username) {
  if (username === 'admin') return 'admin'
  return 'user'
}
```

> Por razones de simplicidad, consideramos que cualquier usuario llamado `admin` tiene el rol de administrador. Todos los demás tienen el rol de usuario normal.

5. Ahora, necesitamos definir un Hook de Estado para el rol:

```javascript
export function Logout({ username, setUsername }) {
  const [role, setRole] = useState('user')
```

6. A continuación, define un Hook de Efecto que establezca el rol llamando a la "API externa" y utilizando su respuesta:

```javascript
  useEffect(() => {
    setRole(getRole(username))
  }, [username])
```

> La mejor práctica consiste en enumerar todos los valores (y funciones) que se utilizan dentro de un Hook de Efecto en el array de dependencias. Esto garantiza que no haya errores accidentales cuando los valores que parecen estáticos en el momento se vuelvan dinámicos más adelante. Afortunadamente, el plugin de ESLint de React Hooks (que ya está configurado en nuestro proyecto) nos avisará si olvidamos agregar una dependencia.

7. Finalmente, mostramos el rol si el usuario tiene un rol especial:

```jsx
  return (
    <form onSubmit={handleSubmit}>
      Logged in as: <b>{username}</b>
      {role !== 'user' ? ` (role: ${role})` : ''}
```

8. Inicia la aplicación de blog ejecutando el siguiente comando:

```bash
npm run dev
```

Intenta iniciar sesión con el nombre de usuario `admin`. ¡Verás que ahora el rol se muestra junto al nombre de usuario!

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter04/Chapter04_2`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Resumen

En este capítulo, primero aprendimos sobre acciones, reducers y Hooks Reducer. También aprendimos cuándo debemos usar Hooks Reducer en lugar de Hooks de Estado. Luego, reemplazamos nuestro Hook de Estado global existente para el estado `posts` por un Hook Reducer. A continuación, aprendimos sobre los Hooks de Efecto y cómo se pueden usar en lugar de los métodos de ciclo de vida `componentDidMount` y `componentDidUpdate`. Finalmente, implementamos la verificación de roles en nuestra aplicación de blog mediante el uso de un Hook de Efecto.

En el próximo capítulo, aprenderemos sobre React Context y cómo usarlo con Hooks. Luego, agregaremos Hooks de Contexto a nuestra aplicación para evitar tener que pasar props a través de múltiples capas de componentes.

---

### Preguntas

Para repasar lo aprendido en este capítulo, intenta responder a las siguientes preguntas:

1. ¿Cuáles son los problemas comunes con los Hooks de Estado?
2. ¿Qué son las acciones?
3. ¿Qué son los reducers?
4. ¿Cuándo deberíamos usar un Hook Reducer en lugar de un Hook de Estado?
5. ¿Qué pasos se necesitan para convertir un Hook de Estado en un Hook Reducer?
6. ¿Cómo podríamos crear acciones más fácilmente?
7. ¿Cuál es el equivalente de los Hooks de Efecto en los componentes de clase?
8. ¿Cuáles son las ventajas de utilizar un Hook de Efecto frente a componentes de clase?
9. ¿Qué es el array de dependencias y cómo funciona?
10. ¿Cómo se pueden usar las funciones de limpieza con los Hooks de Efecto?

---

### Lecturas complementarias

Si estás interesado en obtener más información sobre los conceptos que hemos aprendido en este capítulo, consulta los siguientes enlaces:

- Documentación oficial sobre el Hook Reducer: [https://react.dev/reference/react/useReducer](https://react.dev/reference/react/useReducer)
- Documentación oficial y consejos para el uso de Hooks de Efecto: [https://react.dev/reference/react/hooks#effect-hooks](https://react.dev/reference/react/hooks#effect-hooks)
- Más información sobre cuándo NO se debe utilizar un Hook de Efecto: [https://react.dev/learn/you-might-not-need-an-effect](https://react.dev/learn/you-might-not-need-an-effect)
- Más información sobre Redux, una biblioteca que ofrece una versión más extensa de acciones y reducers: [https://redux.js.org](https://redux.js.org/)
