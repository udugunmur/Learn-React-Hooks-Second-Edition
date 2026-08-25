# Parte 2: Uso de Hooks con ejemplos del mundo real

## Capítulo 7: Uso de Hooks para el manejo de formularios

En el capítulo anterior, aprendimos cómo usar Hooks para la obtención de datos y React Suspense para mostrar una interfaz alternativa mientras esperamos a que los datos terminen de cargarse.

En este capítulo, aprenderemos cómo usar Hooks para manejar formularios y el estado de los formularios en React. Anteriormente ya implementamos un formulario para el componente `CreatePost`. Sin embargo, en lugar de manejar manualmente el envío del formulario, podemos usar **React Form Actions**, las cuales no solo facilitan la gestión del envío de formularios, sino que también nos permiten usar Hooks que acceden al estado del formulario. Además, aprenderemos sobre el Hook `useOptimistic` para implementar **actualizaciones optimistas**, es decir, mostrar el resultado preliminar en el lado del cliente antes de que el servidor termine de procesarlo.

En este capítulo, cubriremos los siguientes temas principales:

- Manejo del envío de formularios con el Hook Action State
- Simulación de una interfaz de usuario bloqueante
- Evitar el bloqueo de la interfaz con el Hook de Transición
- Uso del Hook Optimistic para implementar actualizaciones optimistas

---

### Requisitos técnicos

Debe estar instalada una versión bastante reciente de **Node.js**. El gestor de paquetes de Node (**npm**) también debe estar instalado (debería venir incluido con Node.js). Para obtener más información sobre cómo instalar Node.js, consulta su sitio web oficial: [https://nodejs.org/](https://nodejs.org/).

Utilizaremos **Visual Studio Code (VS Code)** para las guías de este libro, pero todo debería funcionar de manera similar en cualquier otro editor. Para obtener más información sobre cómo instalar VS Code, consulta su sitio web oficial: [https://code.visualstudio.com](https://code.visualstudio.com/).

En este libro, utilizamos las siguientes versiones:

- **Node.js** v22.14.0
- **npm** v10.9.2
- **Visual Studio Code** v1.97.2

Las versiones mencionadas en la lista anterior son las utilizadas en el libro. Aunque instalar una versión más reciente no debería ser un problema, ten en cuenta que ciertos pasos podrían funcionar de manera diferente en una versión más nueva. Si tienes algún problema con el código y los pasos proporcionados en este libro, intenta utilizar las versiones mencionadas.

Puedes encontrar el código de este capítulo en GitHub: [https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter07](https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter07).

Se recomienda encarecidamente que escribas el código por tu cuenta. No te limites a ejecutar simplemente los ejemplos de código proporcionados con el libro. Es importante escribir el código tú mismo para poder aprenderlo y comprenderlo adecuadamente. Sin embargo, si encuentras algún problema, siempre puedes consultar el ejemplo de código.

---

### Manejo del envío de formularios con el Hook Action State

React 19 introdujo una nueva característica llamada **Form Actions**. Como hemos visto en los capítulos anteriores, realizar mutaciones de datos en respuesta a las acciones del usuario es un caso de uso común en las aplicaciones web. A menudo, estas mutaciones de datos requieren realizar una petición a la API y manejar la respuesta, lo que significa lidiar con estados de carga y de error. Por ejemplo, cuando creamos el componente `CreatePost`, creamos un formulario que inserta una nueva publicación en la base de datos al enviarlo. En ese caso, React Query ya nos ayudó simplificando los estados de carga y error. Sin embargo, con React Form Actions ahora existe una forma nativa de manejar estos estados mediante el Hook **Action State** (`useActionState`).

#### Introducción al Hook Action State (*Introducing the Action State Hook*)

El Hook Action State se define de la siguiente manera:

```javascript
const [state, action, isPending] = useActionState(actionFn, initialState)
```

Para definir un Hook Action State, necesitamos proporcionar al menos una función como argumento. Esta función se llamará cuando se envíe el formulario y tiene la siguiente firma:

```javascript
function actionFn(currentState, formData) {
```

La función de acción recibe el estado actual de la acción como primer argumento y los datos del formulario (como un objeto `FormData`) como segundo argumento. Cualquier valor devuelto por la función de acción será el nuevo estado del Hook Action State.

> La **API FormData** es un estándar web utilizado para representar campos de formulario y sus valores. Se puede utilizar para manejar el envío de formularios y enviarlos a través de la red, por ejemplo, mediante `fetch()`. Es un objeto iterable (se puede iterar con un bucle `for … of`) y proporciona funciones de obtención y asignación para acceder a los valores. Puedes encontrar más información en: [https://developer.mozilla.org/en-US/docs/Web/API/FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData).

Adicionalmente, es posible proporcionar un `initialState` para el Hook Action State.

El Hook devuelve:
1. El estado actual de la acción (`state`).
2. La acción en sí misma (`action`), para pasarla al elemento `<form>`.
3. El estado `isPending`, para comprobar si la acción está actualmente pendiente (mientras se ejecuta `actionFn`).

#### Uso del Hook Action State (*Using the Action State Hook*)

Comencemos a refactorizar el componente `CreatePost` para usar el Hook Action State:

1. Copia la carpeta `Chapter06_4` a una nueva carpeta `Chapter07_1` ejecutando el siguiente comando:

```bash
cp -R Chapter06_4 Chapter07_1
```

2. Abre la nueva carpeta `Chapter07_1` en VS Code.
3. Edita `src/components/post/CreatePost.jsx` e importa la función `useActionState`:

```javascript
import { useContext, useActionState } from 'react'
```

4. Dentro del componente `CreatePost`, elimina toda la función `handleSubmit`.
5. Reemplázala con el siguiente Hook Action State:

```javascript
  const [error, submitAction, isPending] = useActionState(
```

> En este caso, utilizaremos el estado de la acción para almacenar un estado de error. Si hubo un error, lo devolveremos desde la función de acción. De lo contrario, no devolvemos nada, por lo que el estado de error será `undefined`.

6. Define la función de acción:

```javascript
    async (currentState, formData) => {
```

> En este caso, no utilizaremos el `currentState` pasado a la función, pero debemos definirlo de todos modos, ya que necesitamos el segundo argumento para obtener `formData`.

7. Obtén el título y el contenido del formulario utilizando la API FormData:

```javascript
      const title = formData.get('title')
      const content = formData.get('content')
```

> La API FormData utiliza la propiedad `name` para identificar los campos de entrada.

8. A continuación, crea el objeto de la publicación y llama a la mutación:

```javascript
      const newPost = { title, content, author: username, featured: false }
      try {
        await createPostMutation.mutateAsync(newPost)
```

> Dado que ahora tenemos una función asíncrona, podemos usar el método `mutateAsync` de la mutación para poder esperar la respuesta con `await`.

9. Si hubo un error, devuélvelo:

```javascript
      } catch (err) {
        return err
      }
    },
  )
```

> Ya no necesitamos restablecer manualmente el formulario. Al utilizar Form Actions, todos los campos no controlados (*uncontrolled fields*) del formulario se restablecerán automáticamente una vez que la función de acción del formulario se complete con éxito.

10. Ajusta el elemento `<form>` para pasarle la acción en lugar de un controlador `onSubmit`:

```jsx
  return (
    <form action={submitAction}>
```

11. Ajusta el botón de envío y el mensaje de error:

```jsx
      <input type='submit' value='Create' disabled={isPending} />
      {error && <div style={{ color: 'red' }}>{error.toString()}</div>}
```

12. Inicia la aplicación:

```bash
npm run dev
```

En la aplicación de blog, inicia sesión y crea una nueva publicación: verás que funciona de la misma manera que antes, ¡pero ahora estamos usando el Hook Action State para el envío del formulario!

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter07/Chapter07_1`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Simulación de una interfaz de usuario bloqueante

Antes de aprender sobre el Hook de Transición (*Transition Hook*), presentemos primero el problema que intenta resolver: el bloqueo de la interfaz de usuario (*blocking UI*). Cuando ciertos componentes son computacionalmente intensivos, renderizarlos puede hacer que toda la interfaz de usuario deje de responder. Esto puede generar una mala experiencia de usuario, ya que los usuarios no pueden realizar ninguna otra acción mientras se renderizan los componentes.

Ahora implementaremos una sección de comentarios en nuestro blog para simular una interfaz de usuario bloqueante.

#### Implementación de un componente Comment (deliberadamente lento)

Comenzamos implementando un componente `Comment`, el cual ralentizaremos a propósito para simular un componente computacionalmente costoso:

1. Copia la carpeta `Chapter07_1` a una nueva carpeta `Chapter07_2` ejecutando el siguiente comando:

```bash
cp -R Chapter07_1 Chapter07_2
```

2. Abre la nueva carpeta `Chapter07_2` en VS Code.
3. Crea una nueva carpeta `src/components/comment/`.
4. Crea un nuevo archivo `src/components/comment/Comment.jsx`. Dentro de él, define y exporta un componente `Comment` que acepte las props `content` y `author`:

```jsx
export function Comment({ content, author }) {
  let startTime = performance.now()
  while (performance.now() - startTime < 1) {
    // do nothing for 1 ms
  }

  return (
    <div style={{ padding: '0.5em 0' }}>
      <span>{content}</span>
      <i> ~ {author}</i>
    </div>
  )
}
```

#### Implementación de un componente CommentList

Ahora implementaremos un componente `CommentList` que renderizará 1000 comentarios:

1. Crea un nuevo archivo `src/components/comment/CommentList.jsx`.
2. Dentro de él, importa el componente `Comment`:

```javascript
import { Comment } from './Comment.jsx'
```

3. Luego, define y exporta un componente `CommentList` que genere 1000 comentarios:

```jsx
export function CommentList() {
  const comments = Array.from({ length: 1000 }, (_, i) => ({
    id: i,
    content: `Comment #${i}`,
    author: 'test',
  }))

  return (
    <div>
      {comments.map((comment) => (
        <Comment {...comment} key={comment.id} />
      ))}
    </div>
  )
}
```

#### Implementación del componente CommentSection

Por último, implementaremos un componente `CommentSection` que nos permitirá mostrar u ocultar los comentarios de una publicación al hacer clic en un botón:

1. Crea un nuevo archivo `src/components/comment/CommentSection.jsx`.
2. Dentro de él, importa la función `useState` de React y el componente `CommentList`:

```javascript
import { useState } from 'react'
import { CommentList } from './CommentList.jsx'
```

3. Define y exporta el componente `CommentSection`, en el cual definimos un Hook de Estado para alternar la visibilidad de la lista de comentarios:

```jsx
export function CommentSection() {
  const [showComments, setShowComments] = useState(false)

  function handleClick() {
    setShowComments((prev) => !prev)
  }

  return (
    <div>
      <button onClick={handleClick}>
        {showComments ? 'Hide' : 'Show'} comments
      </button>
      {showComments && <CommentList />}
    </div>
  )
}
```

4. Edita `src/components/post/Post.jsx` e importa el componente `CommentSection`:

```javascript
import { CommentSection } from '@/components/comment/CommentSection.jsx'
```

5. Renderízalo al final de la publicación:

```jsx
      <i>
        Written by <b>{author}</b>
      </i>
      <br />
      <br />
      <CommentSection />
    </div>
  )
}
```

#### Comprobar la interfaz de usuario bloqueante simulada

Ahora podemos probar la sección de comentarios y ver cómo bloquea la interfaz de usuario:

1. Ejecuta el proyecto:

```bash
npm run dev
```

2. Abre el frontend en tu navegador en `http://localhost:5173/`.
3. Haz clic en uno de los botones **Show comments**.
4. Verás que después de presionar el botón, toda la interfaz no responde. Intenta presionar uno de los otros botones **Show comments**: no funcionará de inmediato.

Renderizar componentes computacionalmente costosos puede hacer que toda la interfaz de usuario deje de responder. Para solucionar esto, necesitamos usar **Transiciones (*Transitions*)**, sobre las cuales aprenderemos en la siguiente sección.

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter07/Chapter07_2`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Evitar el bloqueo de la interfaz con el Hook de Transición

El **Hook de Transición** (`useTransition`) te permite manejar operaciones asíncronas actualizando el estado sin bloquear la interfaz de usuario. Esto es especialmente útil para renderizar árboles de componentes computacionalmente costosos, como renderizar pestañas y su contenido complejo, o al crear un enrutador del lado del cliente.

El Hook de Transición tiene la siguiente firma:

```javascript
const [isPending, startTransition] = useTransition()
```

- El estado `isPending` se puede utilizar para manejar el estado de carga o espera.
- La función `startTransition` nos permite pasar una función sincrónica para iniciar la transición.

Mientras se ejecutan las actualizaciones desencadenadas dentro de la función (por ejemplo, `setState`) y se evalúan sus efectos en los componentes, `isPending` se establecerá en `true`. Esto no bloquea la interfaz de usuario de ninguna manera, por lo que otros componentes se comportan normalmente mientras se ejecuta la transición.

#### Uso del Hook de Transición (*Using the Transition Hook*)

Ahora utilizaremos el Hook de Transición para evitar bloquear la interfaz de usuario al mostrar una gran cantidad de comentarios:

1. Copia la carpeta `Chapter07_2` a una nueva carpeta `Chapter07_3` ejecutando el siguiente comando:

```bash
cp -R Chapter07_2 Chapter07_3
```

2. Abre la nueva carpeta `Chapter07_3` en VS Code.
3. Edita `src/components/comment/CommentSection.jsx` e importa la función `useTransition`:

```javascript
import { useState, useTransition } from 'react'
```

4. Define el Hook de Transición:

```javascript
export function CommentSection() {
  const [showComments, setShowComments] = useState(false)
  const [isPending, startTransition] = useTransition()
```

5. En la función `handleClick`, inicia una transición:

```javascript
  function handleClick() {
    startTransition(() => {
      setShowComments((prev) => !prev)
    })
  }
```

> Las transiciones tienen casos de uso específicos y ciertas limitaciones. Por ejemplo, no utilices Transiciones para manejar el estado de inputs controlados, porque las Transiciones no son bloqueantes, pero en los inputs queremos que el estado se actualice de inmediato. Además, dentro de una Transición, todas las actualizaciones deben llamarse sincrónicamente. Si necesitas esperar una petición asíncrona antes de actualizar el estado, es mejor esperarla con `await` en la función controladora y luego iniciar la Transición. Para obtener más información, consulta la guía en la documentación de React: [https://react.dev/reference/react/useTransition#troubleshooting](https://react.dev/reference/react/useTransition#troubleshooting).

6. Ahora podemos deshabilitar el botón mientras la transición está pendiente:

```jsx
  <button onClick={handleClick} disabled={isPending}>
```

#### Probar la transición no bloqueante

1. Ejecuta el proyecto:

```bash
npm run dev
```

2. Abre el frontend en tu navegador en `http://localhost:5173/`.
3. Haz clic en uno de los botones **Show comments**. Verás que tras presionar el botón, el resto de la interfaz sigue respondiendo sin trabarse. Si presionas otro botón **Show comments**, responderá inmediatamente iniciando otra Transición.

¡El uso de Transiciones nos permite mantener la interfaz de usuario fluida y receptiva mientras provocamos actualizaciones de estado que renderizan componentes computacionalmente costosos!

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter07/Chapter07_3`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Uso del Hook Optimistic para implementar actualizaciones optimistas

Existen dos formas principales de manejar actualizaciones/mutaciones:

1. **Mostrar un estado de carga** y deshabilitar ciertas acciones mientras se carga.
2. **Realizar una actualización optimista (*optimistic update*)**, que muestra de inmediato el resultado de la acción en el cliente mientras la mutación aún está pendiente, y luego actualiza el estado local con el estado del servidor cuando la mutación finaliza.

Por lo general, las actualizaciones optimistas son ideales para acciones rápidas (como una aplicación de chat o redes sociales), mientras que un estado de carga sin actualizaciones optimistas es mejor para acciones críticas (como realizar una transferencia bancaria).

El Hook **Optimistic** (`useOptimistic`) tiene la siguiente firma:

```javascript
const [optimisticState, addOptimistic] = useOptimistic(state, updateFn)
```

Acepta un estado (generalmente el estado del servidor) y una función `updateFn` para procesar la actualización. Devuelve un `optimisticState` y una función `addOptimistic`, que se puede utilizar para añadir optimistamente un nuevo elemento al estado.

La función `updateFn` acepta dos argumentos: el `currentState` y el `optimisticValue` pasado a la función `addOptimistic`. A continuación, devuelve un nuevo estado optimista.

#### Implementación de la creación optimista de comentarios

Implementaremos una forma de crear nuevos comentarios utilizando actualizaciones optimistas:

1. Copia la carpeta `Chapter07_3` a una nueva carpeta `Chapter07_4` ejecutando el siguiente comando:

```bash
cp -R Chapter07_3 Chapter07_4
```

2. Abre la nueva carpeta `Chapter07_4` en VS Code.
3. Crea un nuevo archivo `src/components/comment/CreateComment.jsx` e importa la función `useContext` y `UserContext`:

```jsx
import { useContext } from 'react'
import { UserContext } from '@/contexts/UserContext.js'

export function CreateComment({ addComment }) {
  const [username] = useContext(UserContext)

  async function submitAction(formData) {
    const content = formData.get('content')
    const comment = {
      author: username,
      content,
    }
    await addComment(comment)
  }

  return (
    <form action={submitAction}>
      <input type='text' name='content' />
      <i> ~ {username}</i>
      <input type='submit' value='Create' />
    </form>
  )
}
```

> También es posible definir Form Actions sin utilizar el Hook Action State. Sin embargo, en ese caso solo obtenemos una función simple para manejar el envío del formulario, sin ninguna funcionalidad de gestión de estado del formulario (como estados pendientes y de error).

4. Edita `src/components/comment/CommentList.jsx` y añade las importaciones necesarias:

```javascript
import { useContext, useState, useOptimistic } from 'react'
import { UserContext } from '@/contexts/UserContext.js'
import { CreateComment } from './CreateComment.jsx'
```

5. Elimina el código de generación de los 1000 comentarios:

```javascript
const comments = Array.from({ length: 1000 }, (_, i) => ({
  id: i,
  content: `Comment #${i}`,
  author: 'test',
}))
```

6. Define el Hook de Contexto para obtener el nombre de usuario y un Hook de Estado para almacenar los comentarios:

```javascript
export function CommentList() {
  const [username] = useContext(UserContext)
  const [comments, setComments] = useState([])
```

7. Ahora define el Hook Optimistic:

```javascript
  const [optimisticComments, addOptimisticComment] = useOptimistic(
    comments,
    (state, comment) => [
      ...state,
      {
        ...comment,
        sending: true,
        id: Date.now(),
      },
    ],
  )
```

> En la función de actualización, agregamos el comentario al array con una prop `sending: true`. Usaremos esta propiedad más adelante para distinguir visualmente los comentarios creados optimistamente de los comentarios reales. También definimos un ID temporal para la prop `key`.

8. Define la función `addComment`, que primero agrega el comentario de forma optimista, luego espera un segundo (simulando la petición a la red) y finalmente lo guarda en el estado local:

```javascript
  async function addComment(comment) {
    addOptimisticComment(comment)
    await new Promise((resolve) => setTimeout(resolve, 1000))
    setComments((prev) => [...prev, comment])
  }
```

9. Renderiza los comentarios optimistas y el formulario de creación:

```jsx
  return (
    <div>
      {optimisticComments.map((comment) => (
        <Comment {...comment} key={comment.id} />
      ))}
      {optimisticComments.length === 0 && <i>No comments</i>}
      {username && <CreateComment addComment={addComment} />}
    </div>
  )
}
```

10. Finalmente, edita `src/components/comment/Comment.jsx` y añade la prop `sending`:

```jsx
export function Comment({ content, author, sending }) {
```

11. Elimina el bucle de retraso artificial:

```javascript
let startTime = performance.now()
while (performance.now() - startTime < 1) {
  // do nothing for 1 ms
}
```

12. Ahora, cambia el color dependiendo de la prop `sending`, mostrando los comentarios insertados de forma optimista en color gris:

```jsx
  return (
    <div style={{ padding: '0.5em 0', color: sending ? 'gray' : 'black' }}>
      <span>{content}</span>
      <i> ~ {author}</i>
    </div>
  )
}
```

13. Ejecuta el proyecto:

```bash
npm run dev
```

14. Abre el frontend en `http://localhost:5173/`.
15. Inicia sesión, pulsa **Show comments**, escribe un nuevo comentario y pulsa **Create**.

Verás que el comentario se inserta inmediatamente en color gris y, tras un segundo, pasa a ser de color negro, indicando que se ha guardado correctamente.

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter07/Chapter07_4`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Resumen

En este capítulo, primero aprendimos sobre el manejo del envío y los estados de los formularios utilizando Form Actions y el Hook `useActionState`. Luego, simulamos un problema potencial al tratar con el renderizado de componentes computacionalmente costosos: el bloqueo de la interfaz de usuario. A continuación, resolvimos este problema introduciendo el Hook `useTransition` para cambiar el estado de manera no bloqueante, lo que permite que la interfaz permanezca receptiva mientras los componentes pesados se renderizan. Finalmente, aprendimos a implementar actualizaciones optimistas con `useOptimistic` para mostrar resultados de inmediato mientras esperamos que finalice una operación asíncrona.

En el próximo capítulo, aprenderemos cómo usar Hooks para implementar el enrutamiento del lado del cliente en nuestra aplicación de blog.

---

### Preguntas

Para repasar lo aprendido en este capítulo, intenta responder a las siguientes preguntas:

1. ¿Qué característica podemos usar para manejar el envío de formularios en React 19?
2. ¿Qué estándar web se utiliza para manejar datos de formularios en React 19?
3. ¿Qué Hook se utiliza para manejar diferentes estados de formularios?
4. ¿Cuál es un problema potencial que puede ocurrir al renderizar componentes computacionalmente costosos?
5. ¿Cómo evitamos ese problema?
6. ¿Cuáles son las limitaciones de las Transiciones?
7. ¿Qué Hook podemos usar para mostrar el estado en el cliente antes de que termine de persistir en el servidor?

---

### Lecturas complementarias

Si estás interesado en obtener más información sobre los conceptos que hemos aprendido en este capítulo, consulta los siguientes enlaces:

- Documentación sobre la API FormData: [https://developer.mozilla.org/en-US/docs/Web/API/FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData)
- Envío de formularios con React: [https://react.dev/reference/react-dom/components/form](https://react.dev/reference/react-dom/components/form)
- El Hook Action State (`useActionState`): [https://react.dev/reference/react/useActionState](https://react.dev/reference/react/useActionState)
- El Hook de Transición (`useTransition`): [https://react.dev/reference/react/useTransition](https://react.dev/reference/react/useTransition)
- El Hook Optimistic (`useOptimistic`): [https://react.dev/reference/react/useOptimistic](https://react.dev/reference/react/useOptimistic)
- Más información sobre actualizaciones optimistas: [https://dev.to/_jhohannes/why-your-applications-need-optimistic-updates-3h62](https://dev.to/_jhohannes/why-your-applications-need-optimistic-updates-3h62)
