# Parte 2: Uso de Hooks con ejemplos del mundo real

## Capítulo 10: Uso de Hooks de la comunidad

En el capítulo anterior, aprendimos sobre los diversos Hooks integrados que proporciona React.

En este capítulo, aprenderemos sobre varios Hooks desarrollados por la comunidad. Primero, aprenderemos a usar Hooks para gestionar el estado de la aplicación. Luego, implementaremos *debouncing* mediante Hooks. A continuación, exploraremos diversos Hooks de utilidad. Finalmente, aprenderemos dónde encontrar más Hooks de la comunidad.

En este capítulo, cubriremos los siguientes temas principales:

- Uso de Hooks para gestionar el estado de la aplicación
- Debouncing con Hooks
- Conocer diversos Hooks de utilidad
- Dónde encontrar más Hooks de la comunidad

---

### Requisitos técnicos

Debe estar instalada una versión bastante reciente de **Node.js**. El gestor de paquetes de Node (**npm**) también debe estar instalado (debería venir incluido con Node.js). Para obtener más información sobre cómo instalar Node.js, consulta su sitio web oficial: [https://nodejs.org/](https://nodejs.org/).

Utilizaremos **Visual Studio Code (VS Code)** para las guías de este libro, pero todo debería funcionar de manera similar en cualquier otro editor. Para obtener más información sobre cómo instalar VS Code, consulta su sitio web oficial: [https://code.visualstudio.com](https://code.visualstudio.com/).

En este libro, utilizamos las siguientes versiones:

- **Node.js** v22.14.0
- **npm** v10.9.2
- **Visual Studio Code** v1.97.2

Las versiones mencionadas en la lista anterior son las utilizadas en el libro. Aunque instalar una versión más reciente no debería ser un problema, ten en cuenta que ciertos pasos podrían funcionar de manera diferente en una versión más nueva. Si tienes algún problema con el código y los pasos proporcionados en este libro, intenta utilizar las versiones mencionadas.

Puedes encontrar el código de este capítulo en GitHub: [https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter10](https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter10).

Se recomienda encarecidamente que escribas el código por tu cuenta. No te limites a ejecutar simplemente los ejemplos de código proporcionados con el libro. Es importante escribir el código tú mismo para poder aprenderlo y comprenderlo adecuadamente. Sin embargo, si encuentras algún problema, siempre puedes consultar el ejemplo de código.

---

### Uso de Hooks para gestionar el estado de la aplicación

En esta sección, aprenderemos sobre varios Hooks de la comunidad que te ayudan a administrar el estado de la aplicación. Estos Hooks son proporcionados por [useHooks.com](https://www.usehooks.com/), que es una colección de diversos Hooks útiles empaquetados en una sola biblioteca.

#### useLocalStorage

El Hook Local Storage te permite almacenar y recuperar datos utilizando la API `LocalStorage` del navegador. La API LocalStorage es una forma de almacenar información de forma persistente en el navegador del usuario. Podemos utilizar esto para, por ejemplo, almacenar información sobre el usuario actualmente autenticado.

La función `useLocalStorage` tiene la siguiente firma:

```javascript
const [data, saveData] = useLocalStorage(key, initialValue)
```

El Hook Local Storage acepta una clave `key` (que se utiliza para identificar los datos en el almacenamiento local) y un valor inicial `initialValue` (que es una alternativa utilizada cuando no hay ningún elemento con la clave dada en el almacenamiento local). Luego, devuelve una API similar al Hook de Estado: los datos en sí y una función para actualizar los datos en el almacenamiento local.

En nuestro caso, simplemente almacenaremos el nombre de usuario en el almacenamiento local.

> En una aplicación real, deberías almacenar un token en su lugar, como un JSON Web Token (JWT), e idealmente guardarlo en una Cookie `httpOnly` en lugar de `localStorage`.

Sigue estos pasos para comenzar a almacenar el nombre de usuario en el almacenamiento local:

1. Copia la carpeta `Chapter09_1` a una nueva carpeta `Chapter10_1` ejecutando el siguiente comando:

```bash
cp -R Chapter09_1 Chapter10_1
```

2. Abre la nueva carpeta `Chapter10_1` en VS Code.
3. Instala la biblioteca `useHooks`:

```bash
npm install --save-exact @uidotdev/usehooks@2.4.1
```

4. Elimina el archivo `src/contexts/UserContext.js`. Vamos a reemplazar el `UserContext` por almacenamiento local.
5. Edita `src/App.jsx` y elimina las siguientes importaciones:

```javascript
import { useState } from 'react'
import { UserContext } from './contexts/UserContext.js'
```

Reemplázalas con la importación de `useLocalStorage`:

```javascript
import { useLocalStorage } from '@uidotdev/usehooks'
```

6. Elimina el Hook de Estado `useState('')` en `App.jsx` y reemplázalo por el Hook Local Storage:

```javascript
export function App() {
  const [username] = useLocalStorage('username', null)
```

7. Elimina `UserContext.Provider` envoltorio en `App.jsx`.
8. Edita `src/components/user/UserBar.jsx` y reemplaza el Hook de Contexto por `useLocalStorage`:

```javascript
import { useLocalStorage } from '@uidotdev/usehooks'

export function UserBar() {
  const [username] = useLocalStorage('username', null)
```

9. Edita `src/components/user/Register.jsx` y actualízalo:

```javascript
import { useState } from 'react'
import { useLocalStorage } from '@uidotdev/usehooks'

export function Register() {
  const [, setUsername] = useLocalStorage('username', null)
```

10. Edita `src/components/user/Login.jsx` y actualízalo:

```javascript
import { useLocalStorage } from '@uidotdev/usehooks'

export function Login() {
  const [, setUsername] = useLocalStorage('username', null)
```

11. Edita `src/components/user/Logout.jsx` y actualízalo:

```javascript
import { useState, useEffect } from 'react'
import { useLocalStorage } from '@uidotdev/usehooks'

export function Logout() {
  const [username, setUsername] = useLocalStorage('username', null)
```

12. Edita `src/components/post/CreatePost.jsx` y reemplaza la importación de `UserContext` por `useLocalStorage`:

```javascript
import { useLocalStorage } from '@uidotdev/usehooks'

export function CreatePost() {
  const [username] = useLocalStorage('username', null)
```

13. Edita `src/components/comment/CreateComment.jsx`:

```javascript
import { useLocalStorage } from '@uidotdev/usehooks'

export function CreateComment({ addComment }) {
  const [username] = useLocalStorage('username', null)
```

14. Edita `src/components/comment/CommentList.jsx`:

```javascript
import { useLocalStorage } from '@uidotdev/usehooks'

export function CommentList() {
  const [username] = useLocalStorage('username', null)
```

15. Inicia la aplicación de blog:

```bash
npm run dev
```

Verás que el registro, inicio de sesión y cierre de sesión funcionan de la misma manera que antes, pero ahora con una ventaja añadida: al actualizar la página, el usuario permanece autenticado hasta que pulsa el botón **Logout**.

#### useHistoryState

El Hook History State es una versión extendida del Hook de Estado que añade la funcionalidad de **deshacer (*undo*) / rehacer (*redo*)** los cambios en el estado.

Tiene la siguiente firma:

```javascript
const { state, set, undo, redo, clear, canUndo, canRedo } = useHistoryState(initialState)
```

Le proporcionamos un estado inicial y nos devuelve:
- `state`: El estado actual.
- `set`: Función para establecer el estado.
- `undo`: Función para deshacer cambios.
- `redo`: Función para rehacer cambios.
- `clear`: Función para restablecer el estado al valor inicial.
- `canUndo` y `canRedo`: Banderas booleanas que indican si es posible deshacer o rehacer el estado.

Implementemos la funcionalidad de deshacer/rehacer para nuestro componente `CreatePost`:

1. Edita `src/components/post/CreatePost.jsx` e importa la función `useHistoryState`:

```javascript
import { useLocalStorage, useHistoryState } from '@uidotdev/usehooks'
```

2. Define un Hook History State para el contenido de la publicación:

```javascript
export function CreatePost() {
  const [username] = useLocalStorage('username', null)
  const navigate = useNavigate()
  const { state, set, undo, redo, clear, canUndo, canRedo } = useHistoryState('')
```

3. Define una función controladora para cuando el usuario modifique el contenido:

```javascript
  function handleContentChange(e) {
    const { value } = e.target
    set(value)
  }
```

4. Define botones para deshacer, rehacer y limpiar el contenido:

```jsx
      <div>
        <label htmlFor='create-title'>Title:</label>
        <input type='text' name='title' id='create-title' />
      </div>
      <div>
        <button type='button' disabled={!canUndo} onClick={undo}>
          Undo
        </button>
        <button type='button' disabled={!canRedo} onClick={redo}>
          Redo
        </button>
        <button type='button' onClick={clear}>
          Clear
        </button>
      </div>
```

> Es importante agregar `type='button'` a todos los botones aquí. De lo contrario, al presionar esos botones se enviaría el formulario.

5. Haz que el `<textarea>` sea un elemento controlado proporcionando las props `value` y `onChange`:

```jsx
      <textarea name='content' value={state} onChange={handleContentChange} />
```

6. Por último, dentro del Hook Action State, llama a la función `clear` tras crear la publicación con éxito:

```javascript
  const [error, submitAction, isPending] = useActionState(
    async (currentState, formData) => {
      const title = formData.get('title')
      const content = formData.get('content')
      const post = { title, content, author: username, featured: false }
      try {
        const result = await createPostMutation.mutateAsync(post)
        clear()
        navigate(`/post/${result.id}`)
      } catch (err) {
        return err
      }
    },
  )
```

7. Inicia la aplicación:

```bash
npm run dev
```

Escribe texto en el área de texto: podrás deshacer y rehacer los cambios. Sin embargo, notarás que deshace/rehace un solo carácter a la vez. A continuación, implementaremos *debouncing* para que los cambios se guarden en el historial por bloques de tiempo.

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter10/Chapter10_1`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Debouncing con Hooks

Cuando presionamos deshacer, se deshace un solo carácter a la vez. Para evitar almacenar cada pulsación individual en nuestro historial, necesitamos implementar ***debouncing***, lo que significa que la función que guarda el contenido en el estado del historial solo se llamará después de que no haya habido cambios durante un período determinado de tiempo.

La biblioteca `use-debounce` proporciona un Hook de Debounce básico para valores simples:

```javascript
const [text, setText] = useState('')
const [value] = useDebounce(text, 1000)
```

Para nuestro caso de uso con `useHistoryState`, necesitamos devoluciones de llamada con debounce (*debounced callbacks*):

```javascript
const [text, setText] = useState('')
const [debouncedSet, cancelDebounce] = useDebouncedCallback(
  (value) => setText(value),
  1000
)
```

Si `debouncedSet` se llama varias veces seguidas, el temporizador se reiniciará cada vez, de modo que solo después de 1000 ms sin llamadas adicionales se ejecutará la función `setText`.

#### Debouncing en el editor de publicaciones

Implementemos el debouncing en combinación con el Hook History State en nuestro editor:

1. Copia la carpeta `Chapter10_1` a una nueva carpeta `Chapter10_2` ejecutando el siguiente comando:

```bash
cp -R Chapter10_1 Chapter10_2
```

2. Abre la nueva carpeta `Chapter10_2` en VS Code.
3. Instala la biblioteca `use-debounce`:

```bash
npm install --save-exact use-debounce@10.0.4
```

4. Edita `src/components/post/CreatePost.jsx` e importa las funciones necesarias:

```javascript
import { useActionState, useState, useEffect } from 'react'
import { useDebouncedCallback } from 'use-debounce'
```

5. Define un nuevo Hook de Estado que contendrá el valor del input controlado:

```javascript
  const { state, set, undo, redo, clear, canUndo, canRedo } = useHistoryState('')
  const [content, setContent] = useState('')
```

6. Luego, define un Hook Debounced Callback que actualizará el History State después de 200 ms:

```javascript
  const debounced = useDebouncedCallback((value) => set(value), 200)
```

7. Define un Hook de Efecto que se active cada vez que cambie el History State, cancele el debounce actual y sincronice el input controlado con el valor actual del History State:

```javascript
  useEffect(() => {
    debounced.cancel()
    setContent(state)
  }, [state, debounced])
```

8. Ajusta la función controladora para actualizar inmediatamente el input controlado y activar el debounce para el historial:

```javascript
  function handleContentChange(e) {
    const { value } = e.target
    setContent(value)
    debounced(value)
  }
```

9. Ajusta el `textarea` para usar `content` en lugar de `state` en su prop `value`:

```jsx
      <textarea name='content' value={content} onChange={handleContentChange} />
```

10. Inicia la aplicación:

```bash
npm run dev
```

Ahora, al escribir en el editor, el botón **Undo** se activará después de una breve pausa y deshará fragmentos de texto completos en lugar de caracteres individuales.

#### Diferencia entre valores debounced y valores diferidos (*deferred*)

La principal diferencia entre los valores con *debounce* y los valores diferidos (*deferred*) es que, al aplicar *debounce*, definimos un intervalo de tiempo fijo tras el cual se actualiza el valor. Los valores diferidos (`useDeferredValue`), sin embargo, intentan actualizarse continuamente después de cada cambio en segundo plano sin un temporizador rígido, adaptándose a la velocidad de renderizado del equipo del usuario.

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter10/Chapter10_2`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Conocer diversos Hooks de utilidad

Ahora aprenderemos sobre una selección de Hooks de utilidad proporcionados por la biblioteca `useHooks`.

#### useCopyToClipboard

El Hook Copy To Clipboard facilita la copia de texto al portapapeles en varios navegadores (usando `navigator.clipboard.writeText` o el método tradicional `document.execCommand("copy")` como respaldo).

Tiene la siguiente firma:

```javascript
const [copiedText, copyToClipboard] = useCopyToClipboard()
```

Implementemos un botón para copiar el enlace a una publicación:

1. Copia la carpeta `Chapter10_2` a una nueva carpeta `Chapter10_3` ejecutando el siguiente comando:

```bash
cp -R Chapter10_2 Chapter10_3
```

2. Abre la nueva carpeta `Chapter10_3` en VS Code.
3. Crea un nuevo archivo `src/components/post/CopyLink.jsx`:

```jsx
import { useCopyToClipboard } from '@uidotdev/usehooks'

const CHECKMARK_EMOJI = <>&#9989;</>
const LINK_EMOJI = <>&#128279;</>

export function CopyLink({ url }) {
  const [copiedText, copyToClipboard] = useCopyToClipboard()

  return (
    <button type='button' onClick={() => copyToClipboard(url)}>
      {copiedText ? CHECKMARK_EMOJI : LINK_EMOJI}
    </button>
  )
}
```

4. Edita `src/components/post/Post.jsx`, importa el componente `CopyLink` y renderízalo junto al título:

```jsx
import { CopyLink } from './CopyLink.jsx'

      <h3 style={{ color: theme.primaryColor }}>
        {title} <CopyLink url={window.location.href} />
      </h3>
```

5. Inicia la aplicación:

```bash
npm run dev
```

Al hacer clic en el botón de enlace junto al título en la página de publicación individual, el icono cambiará a un emoji de verificación y copiará la URL al portapapeles.

#### useHover

El Hook Hover rastrea si el usuario está pasando el cursor sobre un elemento. Tiene la siguiente firma:

```javascript
const [ref, hovering] = useHover()
```

Devuelve una `ref` para adjuntar al elemento y un booleano `hovering` que indica el estado del cursor.

Usemos el Hook Hover para mostrar una sugerencia cuando el usuario pase el cursor sobre el botón de copiar enlace:

1. Edita `src/components/post/CopyLink.jsx`:

```jsx
import { useCopyToClipboard, useHover } from '@uidotdev/usehooks'

const CHECKMARK_EMOJI = <>&#9989;</>
const LINK_EMOJI = <>&#128279;</>

export function CopyLink({ url }) {
  const [copiedText, copyToClipboard] = useCopyToClipboard()
  const [ref, hovering] = useHover()

  return (
    <>
      <button ref={ref} type='button' onClick={() => copyToClipboard(url)}>
        {copiedText ? CHECKMARK_EMOJI : LINK_EMOJI}
      </button>
      {hovering && (
        <small>
          <i> {copiedText ? 'copied!' : 'click to copy a link to the post'}</i>
        </small>
      )}
    </>
  )
}
```

Al pasar el cursor sobre el botón, aparecerá el texto de ayuda contextual.

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter10/Chapter10_3`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Dónde encontrar más Hooks de la comunidad

Además de [useHooks.com](https://www.usehooks.com/), existen muchas colecciones útiles de Hooks desarrolladas por la comunidad:

- **Lista de búsqueda de React Hooks:** [https://nikgraf.github.io/react-hooks/](https://nikgraf.github.io/react-hooks/)
- **use-events:** [https://github.com/sandiiarov/use-events](https://github.com/sandiiarov/use-events) (Convierte eventos de JavaScript como posición del ratón, eventos táctiles o clics fuera de un elemento en Hooks).
- **react-use:** [https://github.com/streamich/react-use](https://github.com/streamich/react-use) (Colección extensa de Hooks para sensores como batería/geolocalización, interfaz gráfica, animaciones y efectos secundarios).

---

### Resumen

En este capítulo, primero aprendimos a almacenar datos de forma persistente en el navegador mediante la API LocalStorage utilizando el Hook `useLocalStorage`. Luego, implementamos la funcionalidad de deshacer/rehacer en el componente `CreatePost` utilizando el Hook `useHistoryState`. A continuación, aprendimos sobre el *debouncing* y lo implementamos con `useDebouncedCallback`. Después, exploramos Hooks de utilidad para interactuar con el portapapeles (`useCopyToClipboard`) y detectar eventos de cursor (`useHover`). Finalmente, descubrimos recursos y repositorios donde encontrar más Hooks comunitarios.

En el próximo capítulo, aprenderemos sobre las reglas de los Hooks, sentando las bases necesarias antes de desarrollar nuestros propios Hooks personalizados.

---

### Preguntas

Para repasar lo aprendido en este capítulo, intenta responder a las siguientes preguntas:

1. ¿Qué Hook podemos usar para almacenar información de forma persistente en el navegador?
2. ¿Qué Hook podemos usar para implementar la funcionalidad de deshacer/rehacer?
3. ¿Qué es el *debouncing* y por qué es necesario en ciertas interfaces?
4. ¿Qué Hook podemos usar para aplicar *debouncing* a funciones de devolución de llamada?
5. ¿En qué se diferencia el *debouncing* de valores del diferimiento de valores (*deferred values*)?
6. ¿Dónde podemos encontrar más colecciones de Hooks desarrolladas por la comunidad?

---

### Lecturas complementarias

Si estás interesado en obtener más información sobre los conceptos que hemos aprendido en este capítulo, consulta los siguientes enlaces y libros:

- *Modern Full-Stack React Projects* por Daniel Bugl
- Sitio web oficial de useHooks: [https://usehooks.com](https://usehooks.com)
- Documentación de la biblioteca use-debounce: [https://github.com/xnimorz/use-debounce](https://github.com/xnimorz/use-debounce)
- Colección de React Hooks: [https://nikgraf.github.io/react-hooks/](https://nikgraf.github.io/react-hooks/)
