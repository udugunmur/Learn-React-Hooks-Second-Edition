# Parte 2: Uso de Hooks con ejemplos del mundo real

## Capítulo 9: Hooks avanzados proporcionados por React

En el capítulo anterior, aprendimos sobre la implementación de rutas utilizando React Router. Luego, aprendimos a usar el Hook Params para rutas dinámicas. A continuación, aprendimos a usar el componente `Link` para proporcionar enlaces a diferentes rutas. Finalmente, aprendimos a redirigir programáticamente mediante el Hook Navigation.

En este capítulo, aprenderemos sobre los diversos Hooks integrados que proporciona React. Comenzaremos ofreciendo una descripción general de los Hooks nativos de React y luego exploraremos varios Hooks de utilidad. A continuación, aprenderemos a utilizar Hooks para optimizar el rendimiento de tu aplicación. Finalmente, aprenderemos sobre los Hooks de Efecto avanzados.

Al final de este capítulo, tendrás una visión completa de todos los Hooks integrados que ofrece React.

En este capítulo, cubriremos los siguientes temas principales:

- Descripción general de los Hooks integrados de React
- Uso de Hooks de utilidad
- Uso de Hooks para optimizaciones de rendimiento
- Uso de Hooks para efectos avanzados

---

### Requisitos técnicos

Debe estar instalada una versión bastante reciente de **Node.js**. El gestor de paquetes de Node (**npm**) también debe estar instalado (debería venir incluido con Node.js). Para obtener más información sobre cómo instalar Node.js, consulta el sitio web oficial: [https://nodejs.org/](https://nodejs.org/).

Utilizaremos **Visual Studio Code (VS Code)** para las guías de este libro, pero todo debería funcionar de manera similar en cualquier otro editor. Para obtener más información sobre cómo instalar VS Code, consulta el sitio web oficial: [https://code.visualstudio.com](https://code.visualstudio.com/).

En este libro, utilizamos las siguientes versiones:

- **Node.js** v22.14.0
- **npm** v10.9.2
- **VS Code** v1.97.2

Aunque instalar una versión más reciente no debería ser un problema, ten en cuenta que ciertos pasos podrían funcionar de manera diferente en una versión más nueva. Si tienes algún problema con el código y los pasos proporcionados en este libro, intenta utilizar las versiones mencionadas.

Puedes encontrar el código de este capítulo en GitHub: [https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter09](https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter09).

Se recomienda encarecidamente que escribas el código por tu cuenta. No te limites a ejecutar simplemente los ejemplos de código proporcionados con el libro. Es importante escribir el código tú mismo para poder aprenderlo y comprenderlo adecuadamente. Sin embargo, si encuentras algún problema, siempre puedes consultar el ejemplo de código.

---

### Descripción general de los Hooks integrados de React

React proporciona ciertos Hooks nativos. Ya hemos aprendido sobre los Hooks básicos que proporciona React:

- `useState` en el Capítulo 2, *Uso del Hook de Estado*
- `useEffect` en el Capítulo 4, *Uso de los Hooks Reducer y Effect*
- `useContext` en el Capítulo 5, *Implementación de contextos de React*

Además, React proporciona Hooks más avanzados, que pueden ser muy útiles en ciertos casos de uso. Ya cubrimos los siguientes Hooks avanzados:

- `useReducer` en el Capítulo 4, *Uso de los Hooks Reducer y Effect*
- `useActionState` en el Capítulo 7, *Uso de Hooks para el manejo de formularios*
- `useFormStatus` (similar a `useActionState`)
- `useOptimistic` en el Capítulo 7, *Uso de Hooks para el manejo de formularios*
- `useTransition` en el Capítulo 7, *Uso de Hooks para el manejo de formularios*

Sin embargo, todavía hay más Hooks avanzados que React proporciona:

- `useRef`
- `useImperativeHandle`
- `useId`
- `useSyncExternalStore`
- `useDebugValue`
- `useDeferredValue`
- `useMemo`
- `useCallback`
- `useLayoutEffect`
- `useInsertionEffect`

Primero, recapitulemos y resumamos los Hooks que ya hemos aprendido. Luego, cubriremos todos estos Hooks avanzados que proporciona React y aprenderemos por qué y cómo utilizarlos.

#### useState

El Hook de Estado devuelve un valor que persistirá a través de las rerenderizaciones y una función para actualizarlo. Se le puede pasar un valor para `initialState` como argumento:

```javascript
const [state, setState] = useState(initialState)
```

Llamar a `setState` actualiza el valor y vuelve a renderizar el componente con el valor actualizado. Si el valor no cambia, React no volverá a renderizar el componente.

También se puede pasar una función a la función `setState`, siendo el primer argumento el valor actual:

```javascript
setState(val => val + 1)
```

Además, se puede pasar una función al primer argumento del Hook si el estado inicial es el resultado de un cálculo complejo. En ese caso, la función solo se llamará una vez durante la inicialización del Hook:

```javascript
const [state, setState] = useState(() => {
  return computeInitialState()
})
```

El Hook de Estado es el Hook más omnipresente proporcionado por React.

#### useEffect

El Hook de Efecto acepta una función que contiene código con efectos secundarios, como temporizadores y suscripciones. La función pasada al Hook se ejecutará después de que se complete el renderizado y el componente esté en pantalla:

```javascript
useEffect(() => {
  // do something
})
```

Se puede devolver una función de limpieza desde el Hook, la cual se llamará cuando el componente se desmonte y se utiliza, por ejemplo, para limpiar temporizadores o suscripciones:

```javascript
useEffect(() => {
  const interval = setInterval(() => {}, 100)
  return () => {
    clearInterval(interval)
  }
})
```

La función de limpieza también se llamará si el componente se renderiza varias veces antes de que el efecto se active nuevamente.

Para evitar activar el efecto en cada rerenderización, podemos especificar un array de valores como segundo argumento del Hook. Cuando cualquiera de estos valores cambia, el efecto se activará nuevamente:

```javascript
useEffect(() => {
  // do something when state changes
}, [state])
```

Este array pasado como segundo argumento se llama **array de dependencias (*dependency array*)** del efecto. Si deseas que el efecto solo se active durante el montaje y la limpieza durante el desmontaje, puedes pasar un array vacío como segundo argumento.

#### useContext

El Hook de Contexto acepta un objeto de contexto y devuelve el valor actual para el contexto. Cuando el proveedor de contexto actualiza su valor, el Hook activará una rerenderización con el valor más reciente:

```javascript
const value = useContext(NameOfTheContext)
```

#### useReducer

El Hook Reducer es una versión avanzada del Hook `useState`. Acepta un reducer como primer argumento, el cual es una función con dos argumentos: `state` y `action`. La función reducer devuelve el estado actualizado calculado a partir del estado actual y la acción. Si el reducer devuelve el mismo valor que el estado anterior, React no volverá a renderizar componentes ni activará efectos:

```javascript
const [state, dispatch] = useReducer(reducer, initialState, initFn)
```

Debemos usar el Hook `useReducer` en lugar del Hook `useState` cuando tratamos con cambios de estado complejos. También es más fácil manejar un estado global porque simplemente podemos pasar hacia abajo la función `dispatch` en lugar de múltiples funciones setter.

> La función `dispatch` es estable y no cambiará en las rerenderizaciones, por lo que es seguro omitirla de los arrays de dependencias de `useEffect` o `useCallback`.

Podemos especificar el estado inicial estableciendo el valor `initialState` o especificando una función `initFn` como tercer argumento. Especificar dicha función tiene sentido cuando calcular el estado inicial lleva mucho tiempo o cuando queremos reutilizar la función para restablecer el estado mediante una acción.

#### useActionState

El Hook Action State se define de la siguiente manera:

```javascript
const [state, action, isPending] = useActionState(actionFn, initialState)
```

Para definir un Hook Action State, necesitamos proporcionar una función de acción como primer argumento, la cual tiene la siguiente firma:

```javascript
function actionFn(currentState, formData) {
```

Luego, debemos pasar la prop `action` a un elemento `<form>`. Cuando se envía este formulario, se llama a la función de acción con el estado actual del Hook y los datos `FormData` enviados dentro del formulario.

Además, es posible proporcionar un `initialState` para el Hook y utilizar el valor `isPending` para mostrar un estado de carga mientras se procesa la acción.

#### useFormStatus

El Hook Form Status se define de la siguiente manera:

```javascript
const { pending, data, method, action } = useFormStatus()
```

Se utiliza en los casos en que el envío del formulario no es manejado directamente por nosotros en el cliente. Por ejemplo, si tenemos un backend que maneja el envío del formulario por nosotros o si estamos usando una Server Action para el estado del formulario.

Devuelve un objeto de estado con las siguientes propiedades:

- `pending`: Se establece en `true` si el `<form>` padre se está enviando actualmente.
- `data`: Contiene el objeto `FormData` que está enviando el formulario padre.
- `method`: Se establece en `'get'` o `'post'`, según el método definido en el `<form>` padre.
- `action`: Si se pasó una función de acción al `<form>` padre, contendrá una referencia a ella; de lo contrario, será `null`.

Por ejemplo, se puede utilizar para implementar un botón de envío que se deshabilite mientras el formulario se envía al servidor:

```jsx
import { useFormStatus } from 'react-dom'

function SubmitButton() {
  const { pending } = useFormStatus()
  return <button disabled={pending}>Submit</button>
}

function ExampleForm() {
  return (
    <form>
      <SubmitButton />
    </form>
  )
}
```

> El Hook Form Status solo se puede utilizar en componentes renderizados dentro de un `<form>`. A diferencia de otros Hooks, es el único Hook exportado desde `react-dom` y no desde `react`.

#### useOptimistic

El Hook Optimistic tiene la siguiente firma:

```javascript
const [optimisticState, addOptimistic] = useOptimistic(state, updateFn)
```

Se puede utilizar para actualizar optimistamente un estado mientras esperamos que termine de actualizarse el estado remoto en el servidor. Acepta un estado (generalmente de una petición a una API, como un Query Hook) y una función de actualización. Luego, el Hook devuelve un estado optimista y una función para agregar un estado optimista.

Por ejemplo, se puede utilizar para insertar un nuevo objeto en un array mientras esperamos que el servidor termine de agregarlo:

```javascript
function updateFn(state, newObject) {
  return state.concat({ ...newObject, pending: true })
}
```

Esta función de actualización inserta optimistamente un nuevo objeto, pero le añade una bandera `pending: true`, para que luego podamos renderizar los objetos pendientes de una manera diferente (por ejemplo, ligeramente atenuados en gris).

#### useTransition

El Hook de Transición te permite manejar operaciones asíncronas actualizando el estado sin bloquear la interfaz de usuario. Esto es especialmente útil para renderizar árboles de componentes computacionalmente costosos, como el renderizado de pestañas y su contenido complejo, o al crear un enrutador del lado del cliente. Tiene la siguiente firma:

```javascript
const [isPending, startTransition] = useTransition()
```

El estado `isPending` se puede utilizar para manejar el estado de carga. La función `startTransition` nos permite pasar una función sincrónica para iniciar la transición. Mientras se ejecutan las actualizaciones desencadenadas dentro de la función y se evalúan sus efectos en los componentes, `isPending` se establecerá en `true`. Esto no bloquea la interfaz de usuario, por lo que otros componentes continúan comportándose normalmente mientras se ejecuta la transición.

---

### Uso de Hooks de utilidad

Comenzamos aprendiendo sobre los Hooks de utilidad. Estos son Hooks que nos permiten modelar ciertos casos de uso o ayudarnos al desarrollar nuestros propios Hooks personalizados.

Configuraremos una página de demostración en nuestra aplicación de blog para poder probar los distintos Hooks de utilidad:

1. Copia la carpeta `Chapter08_2` a una nueva carpeta `Chapter09_1` ejecutando el siguiente comando:

```bash
cp -R Chapter08_2 Chapter09_1
```

2. Abre la nueva carpeta `Chapter09_1` en VS Code.
3. Crea una nueva carpeta `src/components/demo/`. Aquí es donde colocaremos nuestros componentes de demostración para probar los diversos Hooks.
4. Crea un nuevo archivo `src/pages/Demo.jsx` con el siguiente contenido:

```jsx
export function Demo() {
  return <h1>Demo Page</h1>
}
```

5. Edita `src/App.jsx` e importa la página `Demo`:

```javascript
import { Demo } from './pages/Demo.jsx'
```

6. Luego, define un nuevo `NavBarLink` para ella:

```jsx
          <BrowserRouter>
            <div style={{ padding: 8 }}>
              <NavBarLink to='/'>Home</NavBarLink>
              {' | '}
              <NavBarLink to='/demo'>Demo</NavBarLink>
```

7. Finalmente, define una ruta para ella:

```jsx
                    <Routes>
                      <Route index element={<Home />} />
                      <Route path='post/:id' element={<ViewPost />} />
                      <Route path='demo' element={<Demo />} />
```

8. Inicia el servidor de desarrollo y mantenlo en ejecución durante todo el capítulo:

```bash
npm run dev
```

Haz clic en el enlace **Demo** en la barra de navegación para abrir la página de demostración.

#### useRef

El Hook Ref devuelve un objeto ref mutable que se puede asignar a un componente o elemento mediante la prop `ref`:

```javascript
const refContainer = useRef(initialValue)
```

Después de asignar el objeto ref a un elemento o componente, se puede acceder al objeto ref a través de `refContainer.current`. Si se establece `initialValue`, `refContainer.current` se inicializará con este valor antes de la asignación.

Los objetos ref se pueden utilizar para varios casos de uso, pero los dos principales son:
1. Obtener una referencia directa a un elemento para acceder a él en el Document Object Model (DOM).
2. Mantener valores mutables que no deben verse afectados por el ciclo de vida de React (por ejemplo, no provocar una rerenderización cuando se modifica el valor).

##### Enfoque automático de un campo de entrada usando useRef (*Auto-focusing an input field using a Ref Hook*)

Podemos usar un Hook Ref para obtener una referencia a un elemento de campo de entrada y luego acceder a su función `focus()` a través del DOM para enfocarlo automáticamente al renderizarse:

1. Crea una nueva carpeta `src/components/demo/useRef/`.
2. Crea un nuevo archivo `src/components/demo/useRef/AutoFocus.jsx`. Dentro de él, importa `useRef` y `useEffect`:

```jsx
import { useRef, useEffect } from 'react'

export function AutoFocus() {
  const inputRef = useRef(null)

  useEffect(() => inputRef.current.focus(), [])

  return (
    <div>
      <h3>AutoFocus</h3>
      <input ref={inputRef} type='text' />
    </div>
  )
}
```

3. Ahora, edita `src/pages/Demo.jsx` e importa el componente `AutoFocus`:

```jsx
import { AutoFocus } from '@/components/demo/useRef/AutoFocus.jsx'

export function Demo() {
  return (
    <div>
      <h1>Demo Page</h1>
      <h2>useRef</h2>
      <AutoFocus />
    </div>
  )
}
```

Actualiza la página; deberías ver que el campo de entrada se enfoca automáticamente.

##### Cambio de estado dentro de una referencia (*Changing state within a ref*)

Es importante tener en cuenta que mutar el valor actual de una ref (`ref.current`) **no provoca una rerenderización**. Si se necesita reaccionar cuando se adjunta el nodo, podemos usar una función de devolución de llamada ref (*ref callback*) en su lugar. Esta función se llamará cuando el elemento se cargue en el DOM:

1. Crea un nuevo archivo `src/components/demo/useRef/InitialWidthMeasure.jsx`:

```jsx
import { useState } from 'react'

export function InitialWidthMeasure() {
  const [width, setWidth] = useState(0)

  function measureRef(node) {
    if (node !== null) {
      setWidth(node.getBoundingClientRect().width)
    }
  }

  return (
    <div>
      <h3>InitialWidthMeasure</h3>
      <div ref={measureRef}>I was initially {Math.round(width)}px wide</div>
    </div>
  )
}
```

2. Edita `src/pages/Demo.jsx` e importa y renderiza el componente `InitialWidthMeasure`:

```jsx
import { InitialWidthMeasure } from '@/components/demo/useRef/InitialWidthMeasure.jsx'

export function Demo() {
  return (
    <div>
      <h1>Demo Page</h1>
      <h2>useRef</h2>
      <AutoFocus />
      <InitialWidthMeasure />
```

##### Uso de referencias para persistir valores mutables a través de rerenderizaciones

Las referencias se pueden usar para acceder al DOM, pero también para conservar valores mutables sin provocar renderizados innecesarios, como almacenar identificadores de intervalos (`setInterval`):

1. Crea un nuevo archivo `src/components/demo/useRef/Timer.jsx`:

```jsx
import { useRef, useState, useEffect } from 'react'

export function Timer() {
  const intervalRef = useRef(null)
  const [seconds, setSeconds] = useState(0)

  function increaseSeconds() {
    setSeconds((prevSeconds) => prevSeconds + 1)
  }

  useEffect(() => {
    intervalRef.current = setInterval(increaseSeconds, 1000)
    return () => clearInterval(intervalRef.current)
  }, [])

  return (
    <div>
      <h3>Timer</h3>
      {seconds} seconds
      <button
        type='button'
        onClick={() => clearInterval(intervalRef.current)}
      >
        Cancel
      </button>
    </div>
  )
}
```

2. Edita `src/pages/Demo.jsx` e importa y renderiza el componente `Timer`:

```jsx
import { Timer } from '@/components/demo/useRef/Timer.jsx'

export function Demo() {
  return (
    <div>
      <h1>Demo Page</h1>
      <h2>useRef</h2>
      <AutoFocus />
      <InitialWidthMeasure />
      <Timer />
```

> El uso de refs en este caso las hace similares a las variables de instancia en las clases (`this.intervalRef`).

##### Pasar referencias como props (*Passing refs as props*)

Desde React 19, ya no es necesario utilizar el asistente `forwardRef` para recibir referencias en componentes personalizados: podemos simplemente pasar `ref` como una prop normal.

1. Crea un nuevo archivo `src/components/demo/useRef/CustomInput.jsx`:

```jsx
export function CustomInput({ ref }) {
  return <input ref={ref} type='text' />
}
```

2. Ahora, edita `src/components/demo/useRef/AutoFocus.jsx` y reemplaza el input estándar por `CustomInput`:

```jsx
import { useRef, useEffect } from 'react'
import { CustomInput } from './CustomInput.jsx'

export function AutoFocus() {
  const inputRef = useRef(null)

  useEffect(() => inputRef.current.focus(), [])

  return (
    <div>
      <h3>AutoFocus</h3>
      <CustomInput ref={inputRef} />
    </div>
  )
}
```

##### Crear el contenido de una referencia solo una vez (*Creating ref contents only once*)

Si tienes un objeto complejo que necesita inicialización (por ejemplo, un algoritmo pesado), puedes inicializarlo perezosamente en una ref:

```javascript
function Map() {
  const pathfinderRef = useRef(null)
  if (pathfinderRef.current === null) {
    pathfinderRef.current = createPathfinder()
  }
}
```

> Llamar directamente a la función dentro del Hook, como `useRef(createPathfinder())`, ejecutaría innecesariamente la función pesada en cada renderizado.

En resumen, las referencias son útiles para:
- Almacenar información entre rerenderizaciones (no se restablecen al renderizar).
- Cambiar información sin activar una nueva renderización.
- Almacenar información local para cada instancia de un componente.

#### useImperativeHandle

El Hook Imperative Handle se puede utilizar para personalizar los métodos o valores de instancia que se exponen a otros componentes cuando se les asigna una `ref`. Esto debe evitarse en la medida de lo posible, ya que acopla estrechamente los componentes.

Tiene la siguiente firma:

```javascript
useImperativeHandle(ref, createHandle, [dependencies])
```

Probemos este Hook exponiendo una función `focus` personalizada que enfoque el campo de entrada y lo resalte en amarillo durante un segundo:

1. Crea una nueva carpeta `src/components/demo/useImperativeHandle/`.
2. Crea un archivo `src/components/demo/useImperativeHandle/HighlightFocusInput.jsx`:

```jsx
import { useImperativeHandle, useRef, useState } from 'react'

export function HighlightFocusInput({ ref }) {
  const inputRef = useRef(null)
  const [highlight, setHighlight] = useState(false)

  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus()
      setHighlight(true)
      setTimeout(() => setHighlight(false), 1000)
    },
  }))

  return (
    <input
      ref={inputRef}
      type='text'
      style={{ backgroundColor: highlight ? 'yellow' : undefined }}
    />
  )
}
```

3. Crea un archivo `src/components/demo/useImperativeHandle/HighlightFocus.jsx`:

```jsx
import { useRef } from 'react'
import { HighlightFocusInput } from './HighlightFocusInput.jsx'

export function HighlightFocus() {
  const inputRef = useRef(null)

  return (
    <div>
      <h3>HighlightFocus</h3>
      <button onClick={() => inputRef.current.focus()}>focus it</button>
      <HighlightFocusInput ref={inputRef} />
    </div>
  )
}
```

4. Edita `src/pages/Demo.jsx` e importa y renderiza `HighlightFocus`:

```jsx
import { HighlightFocus } from '@/components/demo/useImperativeHandle/HighlightFocus.jsx'

      <Timer />
      <h2>useImperativeHandle</h2>
      <HighlightFocus />
    </div>
  )
}
```

#### useId

El Hook Id se utiliza para generar identificadores únicos estables. Esto es muy útil para proporcionar IDs a elementos para atributos de accesibilidad (como `aria-labelledby`, `aria-describedby` o `htmlFor`).

Tiene la siguiente firma:

```javascript
const uniqueId = useId()
```

Probemos a generar un ID único para un campo de casilla de verificación (*checkbox*):

1. Crea una nueva carpeta `src/components/demo/useId/`.
2. Crea un nuevo archivo `src/components/demo/useId/AriaInput.jsx`:

```jsx
import { useId } from 'react'

export function AriaInput() {
  const inputId = useId()

  return (
    <div>
      <h3>AriaInput</h3>
      <label htmlFor={inputId}>
        <input id={inputId} type='checkbox' /> I agree to the Terms and
        Conditions.
      </label>
    </div>
  )
}
```

3. Edita `src/pages/Demo.jsx` e importa y renderiza `AriaInput`:

```jsx
import { AriaInput } from '@/components/demo/useId/AriaInput.jsx'

      <h2>useImperativeHandle</h2>
      <HighlightFocus />
      <h2>useId</h2>
      <AriaInput />
    </div>
  )
}
```

> Si tienes múltiples campos de entrada en un solo componente, la mejor práctica es llamar a `useId()` una sola vez y luego derivar los demás IDs añadiendo sufijos: `${id}-tos-check` y `${id}-username`.

#### useSyncExternalStore

El Hook Sync External Store se utiliza para suscribirse a almacenes de datos externos (*external stores*), como bibliotecas de gestión de estado o APIs del navegador.

Su firma es la siguiente:

```javascript
const snapshot = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot)
```

- `subscribe`: Función que recibe una devolución de llamada y la suscribe al almacén. Debe devolver una función para cancelar la suscripción.
- `getSnapshot`: Función que devuelve una captura instantánea del estado actual de los datos en el almacén.
- `getServerSnapshot`: Función opcional que devuelve la captura inicial durante el renderizado en el servidor (SSR).

Implementemos un indicador que verifique si hay una conexión de red disponible suscribiéndose a la API del navegador:

1. Crea una nueva carpeta `src/components/demo/useSyncExternalStore/`.
2. Crea un archivo `src/components/demo/useSyncExternalStore/OnlineIndicator.jsx`:

```jsx
import { useSyncExternalStore } from 'react'

function subscribe(callback) {
  window.addEventListener('online', callback)
  window.addEventListener('offline', callback)
  return () => {
    window.removeEventListener('online', callback)
    window.removeEventListener('offline', callback)
  }
}

function getSnapshot() {
  return navigator.onLine
}

export function OnlineIndicator() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot)
  const status = isOnline ? 'online' : 'offline'

  return (
    <div>
      <h3>OnlineIndicator</h3>
      {status}
    </div>
  )
}
```

3. Edita `src/pages/Demo.jsx` e importa y renderiza el componente `OnlineIndicator`:

```jsx
import { OnlineIndicator } from '@/components/demo/useSyncExternalStore/OnlineIndicator.jsx'

      <h2>useId</h2>
      <AriaInput />
      <h2>useSyncExternalStore</h2>
      <OnlineIndicator />
    </div>
  )
}
```

#### useDebugValue

El Hook Debug Value es útil al desarrollar Hooks personalizados en bibliotecas compartidas para mostrar etiquetas de depuración en React DevTools:

```javascript
useDebugValue(value, format)
```

Probémoslo extrayendo la lógica del indicador online en un Hook personalizado:

1. Edita `src/components/demo/useSyncExternalStore/OnlineIndicator.jsx`:

```jsx
import { useSyncExternalStore, useDebugValue } from 'react'

function subscribe(callback) {
  window.addEventListener('online', callback)
  window.addEventListener('offline', callback)
  return () => {
    window.removeEventListener('online', callback)
    window.removeEventListener('offline', callback)
  }
}

function getSnapshot() {
  return navigator.onLine
}

function useOnlineStatus() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot)
  const status = isOnline ? 'online' : 'offline'
  useDebugValue(status)
  return status
}

export function OnlineIndicator() {
  const status = useOnlineStatus()
  return (
    <div>
      <h3>OnlineIndicator</h3>
      {status}
    </div>
  )
}
```

Al inspeccionar el componente en las React Developer Tools, verás el valor de depuración asociado al Hook personalizado.

---

### Uso de Hooks para optimizaciones de rendimiento

Ciertos Hooks se pueden utilizar para optimizar el rendimiento de tu aplicación. La regla general es **no optimizar prematuramente**. Con el **React Compiler** introducido en React 19, muchas optimizaciones se realizan de forma completamente automática.

#### useDeferredValue

El Hook Deferred Value se puede utilizar para posponer actualizaciones de baja prioridad (como filtrar una lista) para que las actualizaciones de mayor prioridad (como escribir en un input) se procesen primero de manera fluida:

```javascript
const deferredValue = useDeferredValue(value, initialValue)
```

A diferencia del *debouncing* (donde se establece un tiempo fijo de espera), el diferimiento es dinámico y depende de la velocidad con la que el dispositivo del usuario pueda renderizar la interfaz.

##### Implementación de una búsqueda con useDeferredValue

1. Edita `src/api.js` y define una función de búsqueda con retraso simulado:

```javascript
function sleep(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms))
}

export async function searchPosts(query) {
  const res = await fetch('/api/posts')
  const posts = await res.json()
  const filteredPosts = posts.filter((post) => {
    const title = post.title.toLowerCase()
    return title.includes(query.toLowerCase())
  })
  await sleep(1000)
  return filteredPosts
}
```

2. Crea un archivo `src/components/post/PostSearchResults.jsx`:

```jsx
import { useSuspenseQuery } from '@tanstack/react-query'
import { searchPosts } from '@/api.js'
import { PostList } from './PostList.jsx'

export function PostSearchResults({ query }) {
  const { data } = useSuspenseQuery({
    queryKey: ['posts', query],
    queryFn: () => searchPosts(query),
  })
  return <PostList posts={data} />
}
```

3. Crea un archivo `src/components/post/PostSearch.jsx` utilizando `useDeferredValue`:

```jsx
import { useState, Suspense, useDeferredValue } from 'react'
import { PostSearchResults } from './PostSearchResults.jsx'

export function PostSearch() {
  const [query, setQuery] = useState('')
  const deferredQuery = useDeferredValue(query)

  return (
    <div>
      <input value={query} onChange={(e) => setQuery(e.target.value)} />
      <Suspense fallback={<h4>loading...</h4>}>
        <PostSearchResults query={deferredQuery} />
      </Suspense>
    </div>
  )
}
```

> Al usar `useDeferredValue(query)`, React mantendrá visibles los resultados de búsqueda anteriores mientras se obtienen los nuevos resultados en segundo plano, en lugar de reemplazar inmediatamente toda la interfaz con el mensaje de carga.

4. Crea un archivo `src/pages/Search.jsx`:

```jsx
import { PostSearch } from '@/components/post/PostSearch.jsx'

export function Search() {
  return (
    <div>
      <h1>Search posts</h1>
      <PostSearch />
    </div>
  )
}
```

5. Edita `src/App.jsx`, importa la página `Search` y añade su enlace y ruta:

```javascript
import { Search } from './pages/Search.jsx'
```

```jsx
          <BrowserRouter>
            <div style={{ padding: 8 }}>
              <NavBarLink to='/'>Home</NavBarLink>
              {' | '}
              <NavBarLink to='/search'>Search</NavBarLink>
              {' | '}
              <NavBarLink to='/demo'>Demo</NavBarLink>
```

```jsx
                    <Routes>
                      <Route index element={<Home />} />
                      <Route path='post/:id' element={<ViewPost />} />
                      <Route path='demo' element={<Demo />} />
                      <Route path='search' element={<Search />} />
                    </Routes>
```

#### useMemo

El Hook Memo toma el resultado de una función y lo memoriza para que no se vuelva a calcular en cada renderizado a menos que cambien sus dependencias:

```javascript
const memoizedVal = useMemo(
  () => computeVal(a, b, c),
  [a, b, c]
)
```

> `useMemo` se ejecuta durante el renderizado, por lo que la función no debe realizar efectos secundarios (como llamadas de red). Dado que React 19 cuenta con el React Compiler para memorizaciones automáticas, solo debes recurrir a `useMemo` manual ante problemas de rendimiento específicos no resueltos por el compilador.

#### useCallback

El Hook `useCallback` funciona de manera similar a `useMemo`, pero devuelve una **función de devolución de llamada memorizada** en lugar de un valor:

```javascript
const memoizedCallback = useCallback(
  () => doSomething(a, b, c),
  [a, b, c]
)
```

Es equivalente a:

```javascript
const memoizedCallback = useMemo(
  () => () => doSomething(a, b, c),
  [a, b, c]
)
```

---

### Uso de Hooks para efectos avanzados

Existen dos versiones especializadas del Hook de Efecto:

#### useLayoutEffect

El Hook `useLayoutEffect` es idéntico a `useEffect`, pero **se ejecuta de forma sincrónica** inmediatamente después de que React realiza todas las mutaciones en el DOM y antes de que el navegador pinte la pantalla. Se puede utilizar para medir dimensiones en el DOM y aplicar ajustes de estilo antes de que el usuario vea el fotograma pintado, evitando parpadeos visuales (*layout flickers*).

> `useLayoutEffect` bloquea la pintura visual en el navegador y es más lento que `useEffect`. Úsalo solo si `useEffect` causa parpadeos perceptibles.

#### useInsertionEffect

El Hook `useInsertionEffect` se ejecuta antes de cualquier mutación de diseño (`useLayoutEffect`). Está diseñado específicamente para los creadores de bibliotecas CSS-in-JS (como styled-components o Emotion) para inyectar etiquetas `<style>` dinámicamente antes de calcular el diseño.

#### Código de ejemplo

El código de ejemplo para este capítulo se puede encontrar en la carpeta `Chapter09/Chapter09_1`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Resumen

En este capítulo, aprendimos sobre todos los Hooks proporcionados por React a partir de la versión 19.1. Comenzamos dando una descripción general de los Hooks básicos y avanzados ya conocidos. Luego, aprendimos sobre varios Hooks de utilidad (`useRef`, `useImperativeHandle`, `useId`, `useSyncExternalStore`, `useDebugValue`). A continuación, exploramos los Hooks para la optimización del rendimiento (`useDeferredValue`, `useMemo`, `useCallback`) y el rol del React Compiler. Finalmente, cubrimos los Hooks de Efecto avanzados (`useLayoutEffect`, `useInsertionEffect`).

En el próximo capítulo, aprenderemos a utilizar diversos Hooks desarrollados por la comunidad de React y dónde encontrar más Hooks útiles.

---

### Preguntas

Para repasar lo aprendido en este capítulo, intenta responder a las siguientes preguntas:

1. ¿Cuáles son los diferentes casos de uso del Hook Ref (`useRef`)?
2. ¿Qué funcionalidad añade el Hook Imperative Handle (`useImperativeHandle`)?
3. ¿Cuándo deberíamos usar el Hook Id (`useId`)?
4. ¿Qué caso de uso cubre el Hook Sync External Store (`useSyncExternalStore`)?
5. ¿Cómo podemos usar el Hook Debug Value (`useDebugValue`)?
6. ¿Qué ventajas nos ofrece el Hook Deferred Value (`useDeferredValue`)?
7. ¿Cuándo deberíamos usar los Hooks Memo (`useMemo`) y Callback (`useCallback`)?
8. ¿En la mayoría de los casos, sigue siendo necesario utilizar los Hooks Memo y Callback manualmente en React 19?

---

### Lecturas complementarias

Si estás interesado en obtener más información sobre los conceptos que hemos aprendido en este capítulo, consulta los siguientes enlaces:

- Sección de Hooks integrados de la documentación oficial de React: [https://react.dev/reference/react/hooks](https://react.dev/reference/react/hooks)
- Más información sobre el React Compiler: [https://react.dev/learn/react-compiler](https://react.dev/learn/react-compiler)
