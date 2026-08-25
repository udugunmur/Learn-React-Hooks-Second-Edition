# Parte 2: Uso de Hooks con ejemplos del mundo real

## Capítulo 8: Uso de Hooks para el enrutamiento

En el capítulo anterior, aprendimos cómo usar Hooks para manejar el envío de formularios usando el Hook Action State, cómo evitar el bloqueo de la interfaz con el Hook de Transición y cómo usar el Hook Optimistic para implementar actualizaciones optimistas.

En este capítulo, aprenderemos cómo implementar el enrutamiento del lado del cliente en nuestra aplicación de blog utilizando **React Router**. Primero, aprenderemos cómo funciona React Router y qué características ofrece. Luego, crearemos una nueva ruta para ver una publicación individual y utilizaremos el Hook **Param** (`useParams`) para obtener el ID de la publicación desde la URL. A continuación, aprenderemos a usar el componente `Link` para enlazar diferentes rutas. Finalmente, aprenderemos cómo implementar la navegación programática para redirigir al usuario a una publicación recién creada mediante el Hook **Navigation** (`useNavigate`).

En este capítulo, cubriremos los siguientes temas principales:

- Introducción a React Router
- Creación de una nueva ruta y uso del Hook Param
- Enlazar rutas usando el componente Link
- Navegación programática mediante el Hook Navigation

---

### Requisitos técnicos

Debe estar instalada una versión bastante reciente de **Node.js**. El gestor de paquetes de Node (**npm**) también debe estar instalado (debería venir incluido con Node.js). Para obtener más información sobre cómo instalar Node.js, consulta su sitio web oficial: [https://nodejs.org/](https://nodejs.org/).

Utilizaremos **Visual Studio Code (VS Code)** para las guías de este libro, pero todo debería funcionar de manera similar en cualquier otro editor. Para obtener más información sobre cómo instalar VS Code, consulta su sitio web oficial: [https://code.visualstudio.com](https://code.visualstudio.com/).

En este libro, utilizamos las siguientes versiones:

- **Node.js** v22.14.0
- **npm** v10.9.2
- **Visual Studio Code** v1.97.2

Las versiones mencionadas en la lista anterior son las utilizadas en el libro. Aunque instalar una versión más reciente no debería ser un problema, ten en cuenta que ciertos pasos podrían funcionar de manera diferente en una versión más nueva. Si tienes algún problema con el código y los pasos proporcionados en este libro, intenta utilizar las versiones mencionadas.

Puedes encontrar el código de este capítulo en GitHub: [https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter08](https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter08).

Se recomienda encarecidamente que escribas el código por tu cuenta. No te limites a ejecutar simplemente los ejemplos de código proporcionados con el libro. Es importante escribir el código tú mismo para poder aprenderlo y comprenderlo adecuadamente. Sin embargo, si encuentras algún problema, siempre puedes consultar el ejemplo de código.

---

### Introducción a React Router

React Router comenzó como una biblioteca de enrutamiento declarativa y sencilla. Proporciona funciones para definir y gestionar diferentes rutas para nuestra aplicación, así como para navegar entre ellas. Recientemente, React Router también se puede utilizar como un framework de React, ofreciendo formas de manejar diseños (*layouts*) y renderizado avanzado del lado del servidor. Sin embargo, dado que este libro se centra en los Hooks, nos enfocaremos en React Router como biblioteca.

La biblioteca consta de tres componentes principales:

- **El componente `BrowserRouter`:** proporciona un contexto en el que utilizar el enrutamiento.
- **El componente `Routes`:** nos permite definir rutas y renderiza el componente de la ruta actualmente activa.
- **El componente `Route`:** nos permite definir una ruta específica y el componente que se debe renderizar.

Además, la biblioteca proporciona componentes para crear enlaces a ciertas rutas (usando los componentes `Link` y `NavLink`), así como Hooks para obtener parámetros de la URL (Hook Param) y para navegar (Hook Navigation).

Comencemos configurando React Router y una ruta raíz o de índice (que contendrá la página de inicio de nuestro blog con el feed de publicaciones). La ruta de índice será la que se sirve en la URL principal de nuestro servidor, también llamada a veces punto de entrada o ruta `/`.

#### Configuración de React Router (*Setting up React Router*)

Sigue estos pasos para comenzar a configurar la biblioteca React Router y una ruta de índice:

1. Copia la carpeta `Chapter07_4` a una nueva carpeta `Chapter08_1` ejecutando el siguiente comando:

```bash
cp -R Chapter07_4 Chapter08_1
```

2. Abre la nueva carpeta `Chapter08_1` en VS Code.
3. Abre una terminal e instala la biblioteca `react-router`:

```bash
npm install --save-exact react-router@7.2.0
```

4. Crea una nueva carpeta `src/pages/`, en la que colocaremos las distintas páginas de nuestra aplicación.
5. Crea un nuevo archivo `src/pages/Home.jsx` para contener la página de inicio de nuestra aplicación de blog (que mostrará el feed de publicaciones que ya teníamos anteriormente).
6. Dentro de él, importa `Suspense`, `PostFeed` y `ThemeContext`:

```jsx
import { Suspense } from 'react'
import { PostFeed } from '@/components/post/PostFeed.jsx'
import { ThemeContext } from '@/contexts/ThemeContext.js'

export function Home() {
  return (
    <Suspense fallback={<strong>Loading posts...</strong>}>
      <ThemeContext.Provider value={{ primaryColor: 'salmon' }}>
        <PostFeed featured />
      </ThemeContext.Provider>
      <PostFeed />
    </Suspense>
  )
}
```

7. Edita `src/App.jsx` y elimina la importación de `Suspense`, ya que no la necesitaremos directamente aquí:

```javascript
import { useState } from 'react'
```

8. Además, elimina la importación del componente `PostFeed`:

```javascript
import { PostFeed } from './components/post/PostFeed.jsx'
```

9. Luego, importa `BrowserRouter`, `Routes` y `Route` desde `react-router`:

```javascript
import { BrowserRouter, Routes, Route } from 'react-router'
```

10. Importa también el componente de la página `Home`:

```javascript
import { Home } from './pages/Home.jsx'
```

11. Dentro del componente `App`, define el `BrowserRouter`, asegurándote de que envuelva a todos los componentes para que podamos usar el Hook Navigation en los componentes del encabezado más adelante:

```jsx
export function App() {
  const [username, setUsername] = useState('')

  return (
    <QueryClientProvider client={queryClient}>
      <UserContext.Provider value={[username, setUsername]}>
        <ThemeContext.Provider value={{ primaryColor: 'black' }}>
          <BrowserRouter>
            <div style={{ padding: 8 }}>
              <UserBar />
              <br />
              {username && <CreatePost />}
              <hr />
```

12. Dentro de `ErrorBoundary`, reemplaza el componente `Suspense` y todos sus hijos por el componente `Routes`, en el cual podemos definir las rutas para nuestra aplicación:

```jsx
              <QueryErrorResetBoundary>
                {({ reset }) => (
                  <ErrorBoundary
                    onReset={reset}
                    fallbackRender={FetchErrorNotice}
                  >
                    <Routes>
                      <Route index element={<Home />} />
                    </Routes>
                  </ErrorBoundary>
                )}
              </QueryErrorResetBoundary>
            </div>
          </BrowserRouter>
        </ThemeContext.Provider>
      </UserContext.Provider>
    </QueryClientProvider>
  )
}
```

13. Ejecuta la aplicación:

```bash
npm run dev
```

Al abrir la aplicación en un navegador, verás que se ve exactamente de la misma manera que antes, ¡pero ahora la página de inicio se renderiza a través de React Router en lugar de estar codificada rígidamente!

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter08/Chapter08_1`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Creación de una nueva ruta y uso del Hook Param

Ahora que hemos configurado React Router con éxito, podemos comenzar a crear una nueva ruta para ver una publicación individual. Esta ruta se verá de la siguiente manera: `/post/:id`, siendo `:id` un parámetro de URL (*URL param*) que contiene el identificador de la publicación que se va a visualizar.

> Un parámetro de URL es un parámetro utilizado en una ruta para definir contenido dinámico. Por ejemplo, en la ruta `/post/:id`, la parte `/post/` es una cadena estática, pero `:id` se reemplazará con el ID dinámico de la publicación. Si tienes una URL que termina en `/post/8`, eso significa que la ruta coincide con el parámetro `id` establecido en `8`.

Comencemos a configurar la página y la ruta:

1. Copia la carpeta `Chapter08_1` a una nueva carpeta `Chapter08_2` ejecutando el siguiente comando:

```bash
cp -R Chapter08_1 Chapter08_2
```

2. Abre la nueva carpeta `Chapter08_2` en VS Code.
3. Edita `src/api.js` y define una nueva función para obtener una sola publicación:

```javascript
export async function fetchPost({ id }) {
  const res = await fetch(`/api/posts/${id}`)
  return await res.json()
}
```

4. Edita `src/components/post/Post.jsx` e importa las funciones `useSuspenseQuery` y `fetchPost`:

```javascript
import { useSuspenseQuery } from '@tanstack/react-query'
import { fetchPost } from '@/api.js'
```

5. Cambia el componente `Post` para que solo acepte una prop `id`:

```javascript
export function Post({ id }) {
```

6. Dentro del componente `Post`, agrega un Hook Suspense Query para obtener la publicación y extraer todos sus datos:

```jsx
  const { data } = useSuspenseQuery({
    queryKey: ['post', id],
    queryFn: async () => await fetchPost({ id }),
  })
  const { title, content, author } = data
```

7. Crea un nuevo archivo `src/pages/ViewPost.jsx`. Dentro de él, importa `Suspense`, la función `useParams` desde `react-router` y el componente `Post`:

```jsx
import { Suspense } from 'react'
import { useParams } from 'react-router'
import { Post } from '@/components/post/Post.jsx'

export function ViewPost() {
  const { id } = useParams()

  return (
    <Suspense fallback={<strong>Loading post...</strong>}>
      <Post id={id} />
    </Suspense>
  )
}
```

> Usamos el Hook Param (`useParams`) para obtener el parámetro `id` de la URL.

8. Edita `src/App.jsx` e importa el componente `ViewPost`:

```javascript
import { ViewPost } from './pages/ViewPost.jsx'
```

9. Luego, define una nueva ruta con el parámetro `:id` para la página `ViewPost`:

```jsx
                    <Routes>
                      <Route index element={<Home />} />
                      <Route path='post/:id' element={<ViewPost />} />
                    </Routes>
```

10. Inicia la aplicación (y déjala en ejecución durante el resto del capítulo):

```bash
npm run dev
```

Ahora es posible acceder manualmente a la página de publicación individual añadiendo `/post/:id` a la URL en el navegador (por ejemplo, `http://localhost:5173/post/1`).

Sin embargo, sería conveniente que pudiéramos visitar esta página haciendo clic en una de las publicaciones del feed principal en la página de inicio. Implementemos esto en la siguiente sección utilizando el componente `Link`.

---

### Enlazar rutas usando el componente Link

Cuando tratamos con enlaces en los que el usuario puede hacer clic para visitar una página diferente, lo mejor y más sencillo es utilizar el componente `Link`. Este componente creará automáticamente un enlace estándar accesible a una página específica.

Comencemos a usar el componente `Link` para proporcionar un enlace a una sola publicación:

1. Crea un nuevo archivo `src/components/post/PostListItem.jsx`, en el cual definiremos una versión simplificada del componente `Post` que se mostrará en `PostList`. Dentro de él, importa `useContext`, `ThemeContext` y el componente `Link` desde `react-router`:

```jsx
import { useContext } from 'react'
import { ThemeContext } from '@/contexts/ThemeContext.js'
import { Link } from 'react-router'

export function PostListItem({ id, title, author }) {
  const theme = useContext(ThemeContext)

  return (
    <div>
      <h3 style={{ color: theme.primaryColor }}>{title}</h3>
      <div>
        <Link to={`/post/${id}`}>View Post &gt;</Link>
      </div>
      <br />
      <i>
        Written by <b>{author}</b>
      </i>
    </div>
  )
}
```

2. Edita `src/components/post/PostList.jsx` y reemplaza la importación de `Post` por una importación del componente `PostListItem`:

```javascript
import { PostListItem } from './PostListItem.jsx'
```

3. Renderiza el componente `PostListItem` en lugar del componente `Post`:

```jsx
      {posts.map((post) => (
        <Fragment key={post.id}>
          <PostListItem {...post} />
```

Ahora es posible navegar desde la página de inicio hacia una publicación individual haciendo clic en el enlace **View Post >**.

#### Definición de una barra de navegación usando `<NavLink>`

Si queremos agregar estilos al enlace, por ejemplo, para implementar una barra de navegación donde mostremos en qué página nos encontramos actualmente, podemos usar el componente `NavLink`.

Usemos este componente para implementar una barra de navegación con un enlace para volver a la página de inicio:

1. Crea un nuevo archivo `src/components/NavBarLink.jsx`. Dentro de él, importa el componente `NavLink`:

```jsx
import { NavLink } from 'react-router'

export function NavBarLink({ children, to }) {
  return (
    <NavLink
      to={to}
      style={({ isActive }) => ({
        fontWeight: isActive ? 'bold' : 'normal',
      })}
    >
      {children}
    </NavLink>
  )
}
```

> La prop `style` en `NavLink` recibe una función que nos permite comprobar si el enlace está activo (`isActive`) y aplicar estilos condicionales (como poner el texto en negrita).

2. Edita `src/App.jsx` e importa el componente `NavBarLink`:

```javascript
import { NavBarLink } from './components/NavBarLink.jsx'
```

3. En la sección de encabezado de nuestra aplicación de blog, antes de `UserBar`, define un `NavBarLink` que apunte de regreso a la página de inicio (`/`):

```jsx
          <BrowserRouter>
            <div style={{ padding: 8 }}>
              <NavBarLink to='/'>Home</NavBarLink>
              <hr />
              <UserBar />
```

Ahora disponemos de una forma de ir desde la página de inicio a una publicación individual y volver a la página de inicio para ver otras publicaciones.

---

### Navegación programática mediante el Hook Navigation

Siempre que deseemos navegar de forma programática (mediante código) en lugar de mostrar un enlace para que el usuario haga clic, podemos utilizar el Hook Navigation (`useNavigate`) proporcionado por React Router. Este Hook proporciona una función para navegar mediante código.

Comencemos a usar el Hook Navigation:

1. Edita `src/components/post/CreatePost.jsx` e importa la función `useNavigate`:

```javascript
import { useNavigate } from 'react-router'
```

2. Define el Hook Navigation dentro del componente `CreatePost`:

```javascript
export function CreatePost() {
  const [username] = useContext(UserContext)
  const navigate = useNavigate()
```

3. Dentro del Hook Action State, obtén el resultado de la mutación y redirige a la página `ViewPost` de la publicación recién creada:

```javascript
  const [error, submitAction, isPending] = useActionState(
    async (currentState, formData) => {
      const title = formData.get('title')
      const content = formData.get('content')
      const newPost = { title, content, author: username, featured: false }
      try {
        const result = await createPostMutation.mutateAsync(newPost)
        navigate(`/post/${result.id}`)
      } catch (err) {
        return err
      }
    },
  )
```

Prueba a crear una nueva publicación en la aplicación de blog: ¡verás que se te redirige automáticamente a la página de la publicación recién creada!

Hemos implementado con éxito el enrutamiento en nuestra aplicación de blog. Como ejercicio, podrías intentar implementar los formularios de inicio de sesión/registro y creación de publicaciones en páginas separadas. Al hacerlo, se recomienda refactorizar el enlace de la página de inicio en un nuevo componente `NavBar` con enlaces a las distintas páginas.

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter08/Chapter08_2`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Resumen

En este capítulo, primero aprendimos cómo funciona la biblioteca React Router y de qué componentes consta. Luego, configuramos la biblioteca y una ruta de índice para la página de inicio de nuestro blog (mostrando un feed de publicaciones). A continuación, definimos una nueva ruta para mostrar una sola publicación en una página separada y utilizamos el Hook Params (`useParams`) para obtener el valor del ID desde la URL. Después, aprendimos a navegar a esta nueva ruta y volver a la página de inicio utilizando los componentes `Link` y `NavLink`. Finalmente, aprendimos a navegar programáticamente después de crear una publicación con éxito mediante el Hook Navigation (`useNavigate`).

En el próximo capítulo, aprenderemos sobre los Hooks avanzados incorporados que proporciona React.

---

### Preguntas

Para repasar lo aprendido en este capítulo, intenta responder a las siguientes preguntas:

1. ¿De qué componentes principales consta la biblioteca React Router?
2. ¿Cómo definimos una nueva ruta con la biblioteca React Router?
3. ¿Cómo podemos leer valores dinámicos (parámetros) en las URLs?
4. ¿Cuáles son las formas de definir enlaces con React Router y en qué se diferencian?
5. ¿Qué Hook se utiliza para navegar de forma programática con React Router?

---

### Lecturas complementarias

Si estás interesado en obtener más información sobre los conceptos que hemos aprendido en este capítulo, consulta el siguiente enlace:

- Sitio web oficial de React Router: [https://reactrouter.com/](https://reactrouter.com/)
