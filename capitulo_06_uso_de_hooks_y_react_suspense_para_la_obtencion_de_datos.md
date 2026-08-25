# Parte 2: Uso de Hooks con ejemplos del mundo real

## Capítulo 6: Uso de Hooks y React Suspense para la obtención de datos

En el capítulo anterior, aprendimos a utilizar React Context como alternativa al paso manual de props. Aprendimos sobre proveedores de contexto, consumidores y el Hook de Contexto.

En este capítulo, primero configuraremos un servidor backend simple a partir de un archivo JSON utilizando la herramienta `json-server`. Luego, obtendremos datos de nuestro servidor utilizando un Hook de Efecto en combinación con un Hook de Estado. A continuación, haremos lo mismo utilizando **TanStack Query**, una popular biblioteca de obtención de datos para React que hace uso de Hooks. Finalmente, aprenderemos sobre **React Suspense**, el cual se puede utilizar para posponer el renderizado hasta que el contenido haya terminado de cargarse.

En este capítulo, cubriremos los siguientes temas principales:

- Configuración de un servidor backend simple
- Petición de recursos mediante un Hook de Efecto y un Hook de Estado/Reducer
- Uso de TanStack Query para solicitar recursos y realizar cambios
- Introducción a React Suspense y Error Boundaries

---

### Requisitos técnicos

Debe estar instalada una versión bastante reciente de **Node.js**. El gestor de paquetes de Node (**npm**) también debe estar instalado (debería venir incluido con Node.js). Para obtener más información sobre cómo instalar Node.js, consulta su sitio web oficial: [https://nodejs.org/](https://nodejs.org/).

Utilizaremos **Visual Studio Code (VS Code)** para las guías de este libro, pero todo debería funcionar de manera similar en cualquier otro editor. Para obtener más información sobre cómo instalar VS Code, consulta su sitio web oficial: [https://code.visualstudio.com](https://code.visualstudio.com/).

En este libro, utilizamos las siguientes versiones:

- **Node.js** v22.14.0
- **npm** v10.9.2
- **Visual Studio Code** v1.97.2

Las versiones mencionadas en la lista anterior son las utilizadas en el libro. Aunque instalar una versión más reciente no debería ser un problema, ten en cuenta que ciertos pasos podrían funcionar de manera diferente en una versión más nueva. Si tienes algún problema con el código y los pasos proporcionados en este libro, intenta utilizar las versiones mencionadas.

Puedes encontrar el código de este capítulo en GitHub: [https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter06](https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter06).

Se recomienda encarecidamente que escribas el código por tu cuenta. No te limites a ejecutar simplemente los ejemplos de código proporcionados con el libro. Es importante escribir el código tú mismo para poder aprenderlo y comprenderlo adecuadamente. Sin embargo, si encuentras algún problema, siempre puedes consultar el ejemplo de código.

---

### Configuración de un servidor backend simple

Antes de que podamos implementar peticiones, necesitamos disponer de un servidor. Dado que en este libro nos centramos en la interfaz de usuario, configuraremos un servidor ficticio (*dummy server*) que nos permitirá probar las peticiones. Utilizaremos la herramienta `json-server` para crear una API REST (*Representational State Transfer Application Programming Interface*) simulada a partir de un archivo JSON.

#### Creación del archivo db.json (*Creating the db.json file*)

Para poder utilizar la herramienta `json-server`, primero necesitamos crear un archivo `db.json`, el cual contendrá la base de datos para el servidor. La herramienta `json-server` creará una API REST que nos permitirá acceder y modificar el archivo `db.json`:

- Peticiones **GET**, para ver datos del archivo.
- Peticiones **POST**, para insertar nuevos datos en el archivo.
- Peticiones **PUT** y **PATCH**, para ajustar datos existentes en el archivo.
- Peticiones **DELETE**, para eliminar datos del archivo.

La estructura de la API REST se infiere a partir de un objeto JSON en el archivo `db.json`. Para todas las acciones de modificación (POST, PUT, PATCH y DELETE), la herramienta guardará automáticamente el archivo actualizado.

Podemos utilizar nuestra estructura existente para las publicaciones, la cual definimos como `defaultPosts` en nuestro componente `App`, pero necesitamos proporcionar un valor `id` adicional para poder consultar publicaciones de la base de datos más adelante. Además, le damos a cada publicación un valor `featured`. Esto será importante más adelante para distinguir entre publicaciones destacadas y regulares cuando implementemos la petición:

```json
[
  {
    "id": "1",
    "title": "React Hooks",
    "content": "The greatest thing since sliced bread!",
    "author": "Daniel Bugl",
    "featured": false
  },
  {
    "id": "2",
    "title": "Using React Fragments",
    "content": "Keeping the DOM tree clean!",
    "author": "Daniel Bugl",
    "featured": false
  },
  {
    "id": "3",
    "title": "React Context",
    "content": "Manage global state with ease!",
    "author": "Daniel Bugl",
    "featured": true
  }
]
```

Para los usuarios, debemos definir una forma de almacenar nombres de usuario y contraseñas. Por simplicidad, almacenamos la contraseña en texto plano (¡nunca hagas esto en un entorno de producción!):

```json
[
  {
    "id": "1",
    "username": "Daniel Bugl",
    "password": "hunter2"
  }
]
```

Ahora todo lo que queda por hacer es combinar estos dos arrays en un único objeto JSON, almacenando el array de publicaciones bajo la clave `posts`, y el array de usuarios bajo la clave `users`.

Comencemos a crear el archivo JSON para nuestro servidor backend:

1. Copia la carpeta `Chapter05_2` a una nueva carpeta `Chapter06_1` ejecutando el siguiente comando:

```bash
cp -R Chapter05_2 Chapter06_1
```

2. Abre la nueva carpeta `Chapter06_1` en VS Code.
3. Crea una nueva carpeta `server/`, directamente dentro de la carpeta `Chapter06_1`.
4. Crea un archivo `server/db.json` con el siguiente contenido:

```json
{
  "posts": [
    {
      "id": "1",
      "title": "React Hooks",
      "content": "The greatest thing since sliced bread!",
      "author": "Daniel Bugl",
      "featured": false
    },
    {
      "id": "2",
      "title": "Using React Fragments",
      "content": "Keeping the DOM tree clean!",
      "author": "Daniel Bugl",
      "featured": false
    },
    {
      "id": "3",
      "title": "React Context",
      "content": "Manage global state with ease!",
      "author": "Daniel Bugl",
      "featured": true
    }
  ],
  "users": [
    {
      "id": "1",
      "username": "Daniel Bugl",
      "password": "hunter2"
    }
  ]
}
```

Eso es todo lo que necesitamos para crear automáticamente un backend simple con una API REST utilizando la herramienta `json-server`. Continuemos configurando la herramienta.

#### Instalación de la herramienta json-server

Ahora, instalaremos e iniciaremos nuestro servidor backend utilizando la herramienta `json-server`:

1. Primero, instala la herramienta `json-server`:

```bash
npm install --save-exact json-server@1.0.0-beta.3
```

2. Ahora, inicia el servidor backend ejecutando el siguiente comando:

```bash
npx json-server server/db.json
```

> El comando `npx` ejecuta comandos que se instalaron localmente en un proyecto. Necesitamos usar `npx` aquí porque no instalamos globalmente la herramienta `json-server` (mediante `npm install -g json-server`).

Ejecutamos la herramienta `json-server` y está observando el archivo `server/db.json` que creamos anteriormente.

Por defecto, la herramienta `json-server` define las siguientes rutas para cada clave en el objeto JSON:

```text
GET /posts
GET /posts/:id
POST /posts
PUT /posts/:id
PATCH /posts/:id
DELETE /posts/:id
```

Ahora podemos ir a `http://localhost:3000/posts/1` para ver nuestro objeto de publicación: ¡la herramienta creó una API REST completa a partir del archivo JSON de la base de datos! Ahora, continuemos configurando los scripts de `package.json` para que la herramienta `json-server` se inicie junto con nuestro frontend.

#### Configuración de scripts en package.json (*Configuring the package.json scripts*)

Comencemos ajustando el archivo `package.json`:

1. Edita `package.json` y define un nuevo script llamado `dev:server`, insertándolo en la sección `scripts`. También nos aseguramos de cambiar el puerto para que sea adyacente al puerto predeterminado de Vite (que es 5173):

```json
  "scripts": {
    "dev:server": "json-server server/db.json --port 5174",
```

2. Luego, renombramos el script `dev` a `dev:client`:

```json
  "scripts": {
    "dev:server": "json-server server/db.json --port 5174",
    "dev:client": "vite",
```

3. Guarda el archivo `package.json`; de lo contrario, ejecutar `npm install` más tarde sobrescribirá nuestros cambios.
4. Si todavía se está ejecutando, sal de la herramienta `json-server` presionando `Ctrl+C`.
5. A continuación, instalamos una herramienta llamada `concurrently`, la cual nos permite iniciar el servidor y el cliente al mismo tiempo:

```bash
npm install --save-dev --save-exact concurrently@9.1.2
```

6. Ahora, editamos `package.json` nuevamente y definimos un nuevo script `dev` utilizando el comando `concurrently` y pasando los comandos del servidor y del cliente como argumentos:

```json
  "scripts": {
    "dev": "concurrently \"npm run dev:server\" \"npm run dev:client\"",
```

7. Intenta ejecutar el siguiente comando ahora:

```bash
npm run dev
```

Verás que este comando inicia tanto el servidor como el cliente en paralelo.

Ahora que tenemos tanto el cliente como el servidor ejecutándose, pasemos a configurar un proxy para evitar tener que lidiar con peticiones de origen cruzado (*cross-origin requests*).

#### Configuración de un proxy (*Configuring a proxy*)

Por razones de seguridad, los navegadores imponen restricciones al realizar peticiones a dominios diferentes. Esta restricción se denomina **intercambio de recursos de origen cruzado (CORS - *Cross-Origin Resource Sharing*)** y nos impide realizar peticiones a URLs con un origen diferente. El origen consta del protocolo, dominio y puerto. En nuestro caso, el dominio es el mismo (`localhost`), pero el puerto es diferente (`5173` frente a `5174`). Lo ideal es mantenerse en el mismo dominio y puerto al hacer peticiones desde un frontend hacia un backend. Por lo tanto, necesitamos configurar un proxy que reenvíe las peticiones de `http://localhost:5173/api/` a `http://localhost:5174/`.

Comencemos a configurar el proxy:

1. Edita `vite.config.js` y define una configuración de proxy vinculada a la ruta `/api`:

```javascript
export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: [
      { find: '@', replacement: path.resolve(import.meta.dirname, 'src') },
    ],
  },
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:5174',
        rewrite: (path) => path.replace(/^\/api/, ''),
      },
    },
  },
})
```

Esta configuración de proxy vinculará `/api` a nuestro servidor backend.

2. Cierra el servidor y el cliente si ya se están ejecutando. Luego, inícialos nuevamente con el siguiente comando:

```bash
npm run dev
```

3. Ahora, accede a la API abriendo `http://localhost:5173/api/posts/1` en tu navegador.

Como podemos ver, el objeto de publicación se sigue sirviendo correctamente, ¡pero ahora desde la ruta `/api` a través del proxy definido en Vite!

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter06/Chapter06_1`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

Pasemos ahora a solicitar recursos mediante un Hook de Efecto y un Hook de Estado/Reducer.

---

### Petición de recursos mediante un Hook de Efecto y un Hook de Estado/Reducer

Antes de aprender a usar una biblioteca para implementar peticiones mediante Hooks, las implementaremos manualmente, utilizando un Hook de Efecto para activar la petición y un Hook de Estado/Reducer para almacenar el resultado.

#### Obtener publicaciones desde el servidor (*Fetching posts from the server*)

Ahora implementaremos una forma de obtener publicaciones utilizando un Hook de Efecto. Luego, las almacenaremos ampliando el Hook Reducer ya definido:

1. Copia la carpeta `Chapter06_1` a una nueva carpeta `Chapter06_2` ejecutando el siguiente comando:

```bash
cp -R Chapter06_1 Chapter06_2
```

2. Abre la nueva carpeta `Chapter06_2` en VS Code.
3. Primero, edita `src/reducers.js` y define una nueva acción `FETCH_POSTS`, la cual simplemente devolverá la nueva lista de publicaciones recibida en la acción:

```javascript
export function postsReducer(state, action) {
  switch (action.type) {
    case 'CREATE_POST':
      return [action.post, ...state]
    case 'FETCH_POSTS':
      return action.posts
    default:
      throw new Error('Unknown action type')
  }
}
```

4. Ahora, edita `src/App.jsx` e importa la función `useEffect`:

```javascript
import { useState, useReducer, useEffect } from 'react'
```

5. Elimina los arrays `featuredPosts` y `defaultPosts`.
6. Ajusta el valor predeterminado del Hook Reducer para que sea un array vacío:

```javascript
export function App() {
  const [posts, dispatch] = useReducer(postsReducer, [])
  const [username, setUsername] = useState('')
```

7. Luego, define un Hook de Efecto en el componente `App`:

```javascript
  useEffect(() => {
    fetch('/api/posts')
      .then((response) => response.json())
      .then((posts) => dispatch({ type: 'FETCH_POSTS', posts }))
  }, [])
```

> Pasamos un array vacío al array de dependencias del Hook de Efecto para asegurarnos de que solo se active cuando el componente se monte.

8. Todavía necesitamos separar las publicaciones destacadas de las no destacadas, así que usemos `filter` para dividir el array en dos:

```javascript
  const featuredPosts = posts.filter((post) => post.featured).reverse()
  const regularPosts = posts.filter((post) => !post.featured).reverse()
```

> Invertimos el orden aquí para asegurarnos de que las publicaciones más recientes se muestren primero. Si tuviéramos una propiedad `createdAt`, podríamos usarla en su lugar para ordenar las publicaciones adecuadamente.

9. Pasa `regularPosts` en lugar de `posts` al componente `PostList` para garantizar que las publicaciones destacadas no se rendericen dos veces:

```jsx
  <PostList posts={regularPosts} />
```

10. Inicia el cliente y el servidor:

```bash
npm run dev
```

Ahora, ve a `http://localhost:5173/` en tu navegador.

Para verificar que las publicaciones realmente provienen de nuestra base de datos, realiza un cambio en `db.json` y luego actualiza la página: ¡verás que el cambio es visible en la aplicación!

> En modo de desarrollo, verás dos peticiones GET. Esto se debe a que React renderiza los componentes dos veces en modo estricto (*strict mode*) para ayudarte a detectar efectos secundarios que puedan ocurrir al volver a renderizar los componentes (por ejemplo, olvidar limpiar temporizadores o intervalos). En modo de producción, el componente solo se renderizará una vez y, por lo tanto, solo se enviará una petición GET.

#### Breve desvío: la construcción async/await (*Quick detour: The async/await construct*)

Las funciones regulares se definen de la siguiente manera:

```javascript
function doSomething() {
  // ...
}
```

Las funciones anónimas regulares se definen así:

```javascript
() => {
  // ...
}
```

Las funciones asíncronas se definen agregando la palabra clave `async`:

```javascript
async function doSomething() {
  // ...
}
```

Las funciones anónimas asíncronas también son posibles:

```javascript
async () => {
  // ...
}
```

Dentro de las funciones asíncronas, podemos usar la palabra clave `await` para esperar a que las promesas se resuelvan antes de continuar. En lugar de tener que hacer lo siguiente:

```javascript
function fetchPosts() {
  return fetch('/api/posts')
    .then((response) => response.json())
}
```

Ahora podemos escribir la misma función de esta forma usando `async/await`:

```javascript
async function fetchPosts() {
  const response = await fetch('/api/posts')
  const posts = await response.json()
  return posts
}
```

En la sección anterior, utilizamos la API de Promesas para trabajar con el resultado de una función asíncrona mediante la función `.then()` dentro del Hook de Efecto. Los Hooks de Efecto no admiten pasarles una función `async` directamente para evitar condiciones de carrera (*race conditions*). Sin embargo, es posible definir una función asíncrona dentro del Hook y luego llamarla inmediatamente:

```javascript
useEffect(() => {
  async function fetchPosts() {
    const response = await fetch('/api/posts')
    const posts = await response.json()
    dispatch({ type: 'FETCH_POSTS', posts })
  }
  void fetchPosts()
}, [])
```

> El operador `void` indica explícitamente que no llamamos accidentalmente a una función asíncrona sin `await`. En este caso, queremos invocar la función asíncrona sin necesidad de esperar a que termine dentro del cuerpo del efecto.

Como puedes ver, la construcción `async/await` puede hacer que nuestro código sea más fácil de leer en algunos casos. Puedes elegir cualquier patrón (`then` o `async/await`) según cuál haga que el código sea más legible. Sin embargo, la mejor práctica es no mezclar ambos en la misma función.

#### Creación de nuevas publicaciones en el servidor (*Creating new posts on the server*)

Para crear publicaciones, simplemente necesitamos ajustar la función manejadora de envío para usar `fetch` y realizar una petición POST:

1. Edita `src/components/post/CreatePost.jsx` y haz que la función `handleSubmit` sea asíncrona:

```javascript
  async function handleSubmit(e) {
    e.preventDefault()
    const form = e.target
    const title = form.elements.title.value
    const content = form.elements.content.value
    const newPost = { title, content, author: username, featured: false }
    const response = await fetch('/api/posts', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(newPost),
    })
    if (!response.ok) {
      throw new Error('Unable to create post')
    }
    dispatch({ type: 'CREATE_POST', post: newPost })
    form.reset()
  }
```

2. Crea una nueva publicación mediante el frontend y luego revisa el archivo `server/db.json`: la nueva publicación se insertó correctamente en la base de datos.

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter06/Chapter06_2`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

Ahora que hemos implementado con éxito la obtención y creación de publicaciones utilizando directamente la API Fetch y un Hook de Efecto, podemos pasar a aprender sobre el uso de una biblioteca especializada para solicitar recursos y realizar cambios.

---

### Uso de TanStack Query para solicitar recursos y realizar cambios

En la sección anterior, utilizamos un Hook de Efecto para activar la petición y un Hook Reducer para actualizar el estado utilizando el resultado de la petición. En lugar de implementar manualmente peticiones como esta, podemos usar la biblioteca **TanStack Query**. Esta biblioteca no solo nos permite obtener recursos fácilmente, sino que también almacena en caché el resultado por nosotros y proporciona formas de invalidar el estado. La invalidación nos permite, por ejemplo, volver a solicitar publicaciones del servidor tras crear una nueva publicación, en lugar de tener que despachar manualmente una acción.

#### Configuración de la librería (*Setting up the library*)

Antes de poder comenzar a usarla, necesitamos instalar y configurar la biblioteca. TanStack Query consta de 3 partes:

1. **Un Query Client (*Query Client*):** gestiona la caché y la invalidación.
2. **Un Query Client Provider (*Query Client Provider*):** envuelve tu aplicación para proporcionar el cliente de consultas a todos los componentes.
3. **Una colección de Hooks:** como los Hooks de Consulta (*Query Hooks*) y los Hooks de Mutación (*Mutation Hooks*). El Hook de Consulta se usa para obtener datos y suscribirse a ellos, mientras que el Hook de Mutación se usa cuando necesitas modificar datos en el servidor.

Comencemos a configurar TanStack Query:

1. Copia la carpeta `Chapter06_2` a una nueva carpeta `Chapter06_3` ejecutando el siguiente comando:

```bash
cp -R Chapter06_2 Chapter06_3
```

2. Abre la nueva carpeta `Chapter06_3` en VS Code.
3. Instala la biblioteca TanStack Query:

```bash
npm install --save-exact @tanstack/react-query@5.66.7
```

4. Además, instala el plugin de ESLint como dependencia de desarrollo:

```bash
npm install --save-dev --save-exact @tanstack/eslint-plugin-query@5.66.1
```

5. Edita `eslint.config.js` e importa el plugin:

```javascript
import pluginQuery from '@tanstack/eslint-plugin-query'
```

6. Luego, añade el plugin de la siguiente manera:

```javascript
export default [
  ...pluginQuery.configs['flat/recommended'],
  { ignores: ['dist'] },
```

7. Ahora podemos comenzar a configurar TanStack Query en sí. Primero, crea un nuevo archivo `src/api.js`, que contendrá el Query Client.
8. Edita `src/api.js`, importa y crea el Query Client:

```javascript
import { QueryClient } from '@tanstack/react-query'

export const queryClient = new QueryClient()
```

> Estamos creando una única instancia del cliente de consultas aquí para asegurarnos de que todas las partes de nuestra aplicación utilicen el mismo cliente de consultas (y, por lo tanto, la misma caché).

9. Ahora, edita `src/App.jsx`, elimina las importaciones de `useReducer`, `useEffect` y `postsReducer`:

```javascript
import { QueryClientProvider } from '@tanstack/react-query'
import { queryClient } from './api.js'
```

10. Dentro del componente `App`, elimina los Hooks relacionados con la obtención de publicaciones y envuelve la aplicación con un `QueryClientProvider`:

```jsx
export function App() {
  const [username, setUsername] = useState('')

  return (
    <QueryClientProvider client={queryClient}>
      <UserContext.Provider value={[username, setUsername]}>
        …
      </UserContext.Provider>
    </QueryClientProvider>
  )
}
```

11. Elimina la prop `dispatch` del componente `CreatePost`:

```jsx
      {username && <CreatePost />}
```

> En este punto del capítulo, los arrays `featuredPosts` y `regularPosts` ya no están definidos, lo que provoca errores de ESLint. Ignora estos errores por ahora; los solucionaremos pronto.

¡Ahora estamos listos para usar TanStack Query!

#### Obtener publicaciones mediante un Hook de Consulta (*Fetching posts using a Query Hook*)

Ahora que la biblioteca está configurada, podemos empezar a usarla. Comenzaremos obteniendo publicaciones usando un Hook de Consulta. Para hacer esto, crearemos un nuevo componente `PostFeed`, el cual manejará la lógica de obtención de datos, mientras mantenemos `PostList` como un componente de UI que renderiza una lista de componentes. También definiremos una función que obtendrá publicaciones por nosotros en el archivo `src/api.js`.

Comencemos a obtener publicaciones usando un Hook de Consulta:

1. Edita `src/api.js` y define una nueva función que acepte una prop `featured` y luego obtenga publicaciones por nosotros:

```javascript
export async function fetchPosts({ featured }) {
  const res = await fetch(`/api/posts?featured=${featured}`)
  return await res.json()
}
```

2. Crea un nuevo archivo `src/components/post/PostFeed.jsx`.
3. Dentro de él, importa la función `useQuery`, el componente `PostList` y la función `fetchPosts`:

```jsx
import { useQuery } from '@tanstack/react-query'
import { fetchPosts } from '@/api.js'
import { PostList } from './PostList.jsx'

export function PostFeed({ featured = false }) {
  const { data, isLoading } = useQuery({
    queryKey: ['posts', featured],
    queryFn: () => fetchPosts({ featured }),
  })

  if (isLoading) {
    return <div>Loading posts...</div>
  }

  if (!data) {
    return <div>Could not load posts!</div>
  }

  return <PostList posts={data} />
}
```

> Para cada Hook de Consulta, necesitamos definir una `queryKey`. La `queryKey` se utiliza para almacenar en caché los resultados de una consulta. Si, por ejemplo, consultamos con la misma `queryKey` en otro componente, obtendremos el resultado almacenado en caché en lugar de hacer otra petición. React Query siempre intentará obtener primero el resultado de la caché (si existe para una `queryKey` dada), y si aún no existe en la caché, realizará una petición en segundo plano y la almacenará en caché.

> La `queryKey` también puede ser una fuente de errores cuando se reutiliza accidentalmente para diferentes peticiones. Por ejemplo, necesitamos agregar la prop `featured` a la `queryKey` aquí; de lo contrario, solo se obtendrían y devolverían dos veces las publicaciones destacadas o las regulares. Si obtienes resultados extraños o datos desactualizados de los Query Hooks, asegúrate de verificar tus claves de consulta y de que todos los parámetros pasados a la función de consulta también se agreguen a la clave de consulta.

4. Edita `src/App.jsx` y reemplaza la importación de `PostList` por la importación del componente `PostFeed`:

```javascript
import { PostFeed } from './components/post/PostFeed.jsx'
```

5. Dentro del componente `App`, reemplaza los componentes `PostList` con componentes `PostFeed`:

```jsx
        <hr />
        <ThemeContext.Provider value={{ primaryColor: 'salmon' }}>
          <PostFeed featured />
        </ThemeContext.Provider>
        <PostFeed />
```

#### Creación de publicaciones mediante un Hook de Mutación (*Creating posts using a Mutation Hook*)

Obtener publicaciones requería que hiciéramos una petición para obtener datos del servidor cuando el componente se monta. Sin embargo, para crear publicaciones, queremos hacer una petición al servidor cuando el usuario presiona un botón. Para implementar dicho comportamiento, necesitamos un **Hook de Mutación (*Mutation Hook*)** en lugar de un Hook de Consulta.

Comencemos a implementar la creación de publicaciones utilizando un Hook de Mutación:

1. Edita `src/api.js` y define una nueva función para crear una publicación:

```javascript
export async function createPost(post) {
  const res = await fetch('/api/posts', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(post),
  })

  if (!res.ok) {
    throw new Error('Unable to create post')
  }

  return await res.json()
}
```

2. Edita `src/components/post/CreatePost.jsx`, importa las funciones `useMutation` y `createPost`, así como `queryClient`, y actualiza el componente:

```jsx
import { useContext } from 'react'
import { useMutation } from '@tanstack/react-query'
import { createPost, queryClient } from '@/api.js'
import { UserContext } from '@/contexts/UserContext.js'

export function CreatePost() {
  const [username] = useContext(UserContext)
  const createPostMutation = useMutation({
    mutationFn: createPost,
    onSuccess: () => {
      queryClient.invalidateQueries(['posts'])
    },
  })

  async function handleSubmit(e) {
    e.preventDefault()
    const form = e.target
    const title = form.elements.title.value
    const content = form.elements.content.value
    const newPost = { title, content, author: username, featured: false }

    createPostMutation.mutate(newPost, {
      onSuccess: () => form.reset(),
    })
  }

  return (
    <form onSubmit={handleSubmit}>
      <div>
        Author: <b>{username}</b>
      </div>
      <div>
        <label htmlFor='create-title'>Title:</label>
        <input type='text' name='title' id='create-title' />
      </div>
      <textarea name='content' />
      <input
        type='submit'
        value='Create'
        disabled={createPostMutation.isPending}
      />
      {createPostMutation.isError && (
        <div style={{ color: 'red' }}>
          {createPostMutation.error.toString()}
        </div>
      )}
    </form>
  )
}
```

> Cuando se invalida una clave de consulta, todos los Query Hooks que la utilizan se vuelven a ejecutar automáticamente para obtener los nuevos datos y los componentes se vuelven a renderizar para mostrarla. En este caso, invalidamos todas las claves de consulta que comienzan con `'posts'`, por lo que invalidaremos tanto `['posts', true]` para el feed de publicaciones destacadas como `['posts', false]` para el feed de publicaciones regulares.

Prueba a ejecutar la aplicación y verás que sigue funcionando de la misma manera que antes, ¡pero ahora utilizando TanStack Query!

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter06/Chapter06_3`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Introducción a React Suspense y Error Boundaries

En la sección anterior, utilizamos el estado `isLoading` de TanStack Query para mostrar un mensaje de carga mientras las publicaciones aún se estaban obteniendo. Si bien esto funciona bien, manejar los estados de carga de esa manera puede volverse desordenado. Una mejor manera de modelar los estados de carga es usar **React Suspense**. React Suspense es un componente especial que puede mostrar una interfaz alternativa (*fallback*) hasta que sus componentes hijos hayan terminado de cargarse. Para usar React Suspense, los frameworks y las bibliotecas de obtención de datos deben admitirlo. Afortunadamente, TanStack Query admite Suspense de forma nativa.

#### Configuración de un límite de Suspense (*Setting up a Suspense Boundary*)

Para usar Suspense, necesitamos definir un límite de Suspense (*Suspense Boundary*) con un *fallback*. Si algún componente hijo dentro del límite está obteniendo datos, el *fallback* se renderizará en lugar del límite, reemplazando a todos sus componentes hijos. Cuando todos los datos se hayan obtenido con éxito, se renderizarán todos los componentes hijos. Esto nos permite escribir código que asume que los datos siempre están presentes y manejar el caso de carga más arriba en el árbol.

Comencemos a configurar un límite de Suspense para el feed de publicaciones:

1. Copia la carpeta `Chapter06_3` a una nueva carpeta `Chapter06_4` ejecutando el siguiente comando:

```bash
cp -R Chapter06_3 Chapter06_4
```

2. Abre la nueva carpeta `Chapter06_4` en VS Code.
3. Edita `src/App.jsx` e importa `Suspense`:

```javascript
import { useState, Suspense } from 'react'
```

4. Ajusta el componente `App` para renderizar el feed de publicaciones dentro de un límite de Suspense, proporcionando un mensaje de carga como alternativa:

```jsx
        <Suspense fallback={<strong>Loading posts...</strong>}>
          <ThemeContext.Provider value={{ primaryColor: 'salmon' }}>
            <PostFeed featured />
          </ThemeContext.Provider>
          <PostFeed />
        </Suspense>
```

5. Ahora, necesitamos ajustar el componente `PostFeed` para usar el Hook `useSuspenseQuery` en su lugar. Edita `src/components/post/PostFeed.jsx`:

```jsx
import { useSuspenseQuery } from '@tanstack/react-query'
import { fetchPosts } from '@/api.js'
import { PostList } from './PostList.jsx'

export function PostFeed({ featured = false }) {
  const { data } = useSuspenseQuery({
    queryKey: ['posts', featured],
    queryFn: () => fetchPosts({ featured }),
  })

  return <PostList posts={data} />
}
```

6. Inicia la aplicación:

```bash
npm run dev
```

Verás que, en lugar de recibir dos mensajes de carga independientes (uno para las publicaciones destacadas y otro para las regulares), ¡ahora solo vemos un único mensaje de carga desde el límite de Suspense!

> Para simular una conexión de red más lenta y ver el estado de carga con claridad, puedes abrir las Herramientas de Desarrollo en Google Chrome (Inspeccionar -> pestaña Network -> desplegable de Throttling -> seleccionar **3G**).

#### Configuración de un Error Boundary (*Setting up an Error Boundary*)

Como hemos aprendido, un límite de Suspense puede proporcionar una alternativa mientras los componentes obtienen datos. Sin embargo, para proporcionar una alternativa cuando ocurre un error en un componente hijo, podemos usar un **Error Boundary**. Los Error Boundaries funcionan de manera similar a los límites de Suspense, pero reaccionan a estados de error en lugar de estados de carga.

Comencemos a configurar un Error Boundary:

1. Primero, instala el paquete `react-error-boundary`:

```bash
npm install --save-exact react-error-boundary@5.0.0
```

2. Crea un nuevo archivo `src/FetchErrorNotice.jsx`. Dentro de él, define un componente que reciba la función `resetErrorBoundary`:

```jsx
export function FetchErrorNotice({ resetErrorBoundary }) {
  return (
    <div>
      <strong>There was an error fetching data.</strong>
      <br />
      <button onClick={resetErrorBoundary}>Try again</button>
    </div>
  )
}
```

> La función `resetErrorBoundary` se puede utilizar para restablecer la operación que causó el error. En nuestro caso, reintentará la petición para obtener publicaciones.

3. Ahora, edita `src/App.jsx` e importa `ErrorBoundary`, `QueryErrorResetBoundary` y `FetchErrorNotice`:

```javascript
import { ErrorBoundary } from 'react-error-boundary'
import {
  QueryClientProvider,
  QueryErrorResetBoundary,
} from '@tanstack/react-query'
import { FetchErrorNotice } from './FetchErrorNotice.jsx'
```

4. Dentro del componente `App`, envuelve el límite de Suspense con un `ErrorBoundary`, el cual a su vez está envuelto por `QueryErrorResetBoundary`, que proporciona la función `reset` para reintentar las consultas:

```jsx
      <QueryErrorResetBoundary>
        {({ reset }) => (
          <ErrorBoundary
            onReset={reset}
            fallbackRender={FetchErrorNotice}
          >
            <Suspense fallback={<strong>Loading posts...</strong>}>
              <ThemeContext.Provider value={{ primaryColor: 'salmon' }}>
                <PostFeed featured />
              </ThemeContext.Provider>
              <PostFeed />
            </Suspense>
          </ErrorBoundary>
        )}
      </QueryErrorResetBoundary>
```

5. Si se está ejecutando actualmente, detén la aplicación.
6. Luego, inicia solo el cliente:

```bash
npm run dev:client
```

Abre la aplicación en tu navegador; verás el mensaje de carga. Espera un momento hasta que la petición agote el tiempo de espera (*timeout*). Luego, verás el mensaje de error y el botón de reintento.

7. Ahora, sin cerrar el cliente, inicia adicionalmente el servidor:

```bash
npm run dev:server
```

Presiona el botón **Try again**. ¡Verás el mensaje de carga nuevamente y luego la lista de publicaciones cargada con éxito!

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter06/Chapter06_4`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Resumen

En este capítulo, primero aprendimos cómo configurar un servidor de API simple a partir de un archivo JSON. Luego, aprendimos a obtener y crear publicaciones mediante Hooks de Efecto y Hooks de Estado/Reducer. A continuación, implementamos la misma funcionalidad utilizando la biblioteca TanStack Query, lo que simplificó nuestro código y nos permitió aprovechar sus capacidades de almacenamiento en caché. Finalmente, aprendimos cómo gestionar estados de carga mediante React Suspense y estados de error mediante Error Boundaries.

En el próximo capítulo, aprenderemos en profundidad sobre el manejo de formularios mediante el uso de Form Actions y Hooks, tales como el Hook `useActionState` para manejar estados de formulario y el Hook `useOptimistic` para implementar actualizaciones optimistas.

---

### Preguntas

Para repasar lo aprendido en este capítulo, intenta responder a las siguientes preguntas:

1. ¿Cómo podemos crear fácilmente una API REST completa a partir de un archivo JSON para fines de prueba o simulación (*mocking*)?
2. ¿Cuáles son las ventajas de utilizar un proxy para acceder a nuestro servidor backend?
3. ¿Qué combinaciones de Hooks se pueden utilizar para implementar la obtención de datos?
4. ¿Qué ventajas tiene TanStack Query sobre nuestra implementación simple de obtención de datos?
5. ¿Qué Hook en TanStack Query se utiliza para obtener datos?
6. ¿Qué Hook en TanStack Query se utiliza para realizar cambios en el servidor?
7. ¿Qué papel juega la clave de consulta (*query key*) en la biblioteca TanStack Query?
8. ¿Para qué se utiliza un límite de Suspense (*Suspense Boundary*)?
9. ¿Para qué se utilizan los Error Boundaries?

---

### Lecturas complementarias

Si estás interesado en obtener más información sobre los conceptos que hemos aprendido en este capítulo, consulta los siguientes enlaces:

- Documentación oficial de la herramienta `json-server`: [https://github.com/typicode/json-server](https://github.com/typicode/json-server)
- Documentación oficial de la herramienta `concurrently`: [https://github.com/open-cli-tools/concurrently](https://github.com/open-cli-tools/concurrently)
- Documentación oficial de TanStack Query para React: [https://tanstack.com/query/latest/docs/framework/react/overview](https://tanstack.com/query/latest/docs/framework/react/overview)
- Más información sobre el intercambio de recursos de origen cruzado (CORS): [https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)
- Más información sobre la configuración de proxy en Vite: [https://vite.dev/config/server-options#server-proxy](https://vite.dev/config/server-options#server-proxy)
- Más información sobre el modo estricto de React (*StrictMode*): [https://react.dev/reference/react/StrictMode](https://react.dev/reference/react/StrictMode)
- Artículo sobre obtención de datos con React Hooks sin bibliotecas: [https://www.robinwieruch.de/react-hooks-fetch-data/](https://www.robinwieruch.de/react-hooks-fetch-data/)
- Más información sobre Suspense: [https://react.dev/reference/react/Suspense](https://react.dev/reference/react/Suspense)
- Más información sobre Error Boundaries: [https://github.com/bvaughn/react-error-boundary](https://github.com/bvaughn/react-error-boundary)
