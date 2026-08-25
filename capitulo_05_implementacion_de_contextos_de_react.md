# Parte 2: Uso de Hooks con ejemplos del mundo real

## Capítulo 5: Implementación de contextos de React

En los capítulos anteriores, aprendimos sobre el Hook de Estado, el Hook Reducer y el Hook de Efecto. Desarrollamos una pequeña aplicación de blog utilizando estos Hooks. Como pudimos notar durante el desarrollo de nuestra aplicación, tuvimos que pasar el estado `username` desde el componente `App` al componente `UserBar`, y desde el componente `UserBar` a los componentes `Login`, `Register` y `Logout`. Para evitar tener que pasar el estado de esta manera, ahora vamos a aprender sobre **React Context** y los **Hooks de Contexto (*Context Hooks*)**.

Comenzaremos aprendiendo qué es React Context y qué son los proveedores (*providers*) y consumidores (*consumers*), implementando temas de color (*themes*) como ejemplo de uso de contextos. Luego, utilizaremos Hooks como consumidores de contexto y analizaremos cuándo se deben utilizar los contextos. Finalmente, implementaremos el estado global utilizando contextos.

En este capítulo, cubriremos los siguientes temas principales:

- Introducción a React Context
- Implementación de temas mediante contexto
- Alternativas a los contextos
- Uso del contexto para el estado global

---

### Requisitos técnicos

Debe estar instalada una versión bastante reciente de **Node.js**. El gestor de paquetes de Node (**npm**) también debe estar instalado (debería venir incluido con Node.js). Para obtener más información sobre cómo instalar Node.js, consulta el sitio web oficial: [https://nodejs.org/](https://nodejs.org/).

Utilizaremos **Visual Studio Code (VS Code)** para las guías de este libro, pero todo debería funcionar de manera similar en cualquier otro editor. Para obtener más información sobre cómo instalar VS Code, consulta el sitio web oficial: [https://code.visualstudio.com](https://code.visualstudio.com/).

En este libro, utilizamos las siguientes versiones:

- **Node.js** v22.14.0
- **npm** v10.9.2
- **VS Code** v1.97.2

Aunque instalar una versión más reciente no debería ser un problema, ten en cuenta que ciertos pasos podrían funcionar de manera diferente en una versión más nueva. Si tienes algún problema con el código y los pasos proporcionados en este libro, intenta utilizar las versiones mencionadas.

Puedes encontrar el código de este capítulo en GitHub: [https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter05](https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter05).

Se recomienda encarecidamente que escribas el código por tu cuenta. No te limites a ejecutar simplemente los ejemplos de código proporcionados con el libro. Es importante escribir el código tú mismo para poder aprenderlo y comprenderlo adecuadamente. Sin embargo, si encuentras algún problema, siempre puedes consultar el ejemplo de código.

---

### Introducción a React Context

En los capítulos anteriores, pasamos el estado `username` y la función `setUsername` desde el componente `App` al componente `UserBar`; y luego desde el componente `UserBar` a los componentes `Logout`, `Login` y `Register`. React Context proporciona una solución a esta forma engorrosa de pasar props a través de múltiples niveles de componentes (*prop drilling*), permitiéndonos compartir valores entre componentes sin tener que pasarlos explícitamente a través de props. Como veremos, los contextos son ideales para compartir el estado global en toda la aplicación.

#### Pasar props hacia abajo (*Passing down props*)

Antes de aprender sobre React Context en profundidad, recapitulemos lo que implementamos en los capítulos anteriores para comprender el problema que resuelven los contextos. No necesitas editar ningún código en este punto; estos pasos son solo un repaso de lo que ya hemos hecho:

1. En `src/App.jsx`, definimos el estado `username` y la función `setUsername` utilizando un Hook de Estado:

```javascript
export function App() {
  const [posts, dispatch] = useReducer(postsReducer, defaultPosts)
  const [username, setUsername] = useState('')
```

2. Luego, pasamos el estado `username` y la función `setUsername` al componente `UserBar`:

```jsx
  return (
    <div style={{ padding: 8 }}>
      <UserBar username={username} setUsername={setUsername} />
```

3. En el archivo `src/user/UserBar.jsx`, definimos un componente `UserBar` que recibe el estado `username` como prop y luego lo pasa al componente `Logout`. También pasamos la función `setUsername` a los componentes `Logout`, `Login` y `Register`:

```javascript
export function UserBar({ username, setUsername }) {
  if (username) {
    return <Logout username={username} setUsername={setUsername} />
  } else {
    return (
      <>
        <Login setUsername={setUsername} />
        <hr />
        <Register setUsername={setUsername} />
      </>
    )
  }
}
```

4. Finalmente, utilizamos la función `setUsername` y el estado `username` en los componentes `Logout`, `Login` y `Register`:

```javascript
export function Login({ setUsername }) {
  function handleSubmit(e) {
    e.preventDefault()
    const username = e.target.elements.username.value
    setUsername(username)
  }
```

React Context nos permite omitir los pasos 2 y 3 y saltar directamente del paso 1 al paso 4. Como puedes imaginar, en aplicaciones más grandes, el contexto se vuelve aún más útil porque podríamos tener que pasar props a través de muchos niveles jerárquicos.

En la siguiente sección, primero aprenderemos cómo funciona el contexto implementando un sistema de temas (*theming*) para nuestro blog. Luego, utilizaremos React Context para manejar el estado global `username` en nuestra aplicación de blog.

---

### Implementación de temas mediante contexto

React Context se utiliza para compartir valores en un árbol de componentes de React. Por lo general, queremos compartir valores globales, como el estado de nombre de usuario, el tema de nuestra aplicación o el idioma seleccionado (si la aplicación admite múltiples idiomas).

React Context consta de tres partes:

- **El contexto en sí mismo:** define un valor predeterminado y nos permite proporcionar y consumir valores.
- **El proveedor (*provider*):** proporciona (establece) el valor.
- **El consumidor (*consumer*):** consume (utiliza) el valor.

#### Definición del contexto (*Defining the context*)

Primero, tenemos que definir el contexto. La forma en que funciona no ha cambiado desde que se introdujeron los Hooks. Simplemente usamos la función `createContext(defaultValue)` de React para crear un nuevo objeto de contexto. En este caso, estableceremos el valor predeterminado en `{ primaryColor: 'maroon' }`, por lo que nuestro color primario predeterminado, cuando no se defina ningún proveedor, será granate (*maroon*).

Comencemos a definir el contexto:

1. Copia la carpeta `Chapter04_2` a una nueva carpeta `Chapter05_1` ejecutando el siguiente comando:

```bash
cp -R Chapter04_2 Chapter05_1
```

2. Abre la nueva carpeta `Chapter05_1` en VS Code.
3. Para mantener nuestro proyecto ordenado a medida que crece, ampliaremos la estructura de carpetas agrupando primero por tipos de elementos base y luego por características dentro de esa carpeta. Crea una nueva carpeta `src/contexts/` ahora.
4. Además, crea una nueva carpeta `src/components/`.
5. Mueve las carpetas `src/post/` y `src/user/` dentro de la carpeta `src/components/`.
6. Edita `src/App.jsx` y ajusta las importaciones de la siguiente manera:

```javascript
import { UserBar } from './components/user/UserBar.jsx'
import { CreatePost } from './components/post/CreatePost.jsx'
import { PostList } from './components/post/PostList.jsx'
```

7. Crea un nuevo archivo `src/contexts/ThemeContext.js`. Dentro de él, importa la función `createContext`:

```javascript
import { createContext } from 'react'
```

8. Ahora, define el contexto con el valor predeterminado mencionado anteriormente:

```javascript
export const ThemeContext = createContext({ primaryColor: 'maroon' })
```

> Cuando el contexto se consume pero no se ha definido ningún proveedor, devolverá este valor predeterminado.

Ten en cuenta que estamos exportando `ThemeContext` aquí porque necesitaremos importarlo más adelante para crear el proveedor y consumirlo utilizando un Hook de Contexto.

Eso es todo lo que necesitamos hacer para definir un contexto con React.

#### Breve desvío: importaciones absolutas (*Quick detour – absolute imports*)

Si tuviéramos que importar el contexto en un componente ahora, tendríamos que importar desde `../../contexts/ThemeContext.js`. Además del hecho de que se vuelve difícil de leer cuando los archivos están profundamente anidados, puede causar problemas al organizar archivos en subcarpetas más adelante. Para evitar estos inconvenientes, podemos utilizar importaciones absolutas. Las importaciones absolutas nos permiten importar desde la raíz de un proyecto. Se implementan utilizando alias de resolución (*resolve aliases*) en Vite. Básicamente, podemos indicarle a Vite que resuelva un carácter especial, como el símbolo `@`, a una ruta absoluta hacia la carpeta `src`. Esto significa que podemos importar el contexto desde `@/contexts/ThemeContext.js` en su lugar.

Comencemos a configurar las importaciones absolutas:

1. Edita `vite.config.js` e importa las utilidades de ruta:

```javascript
import path from 'node:path'
```

2. En el objeto de configuración, agrega un alias de resolución de la siguiente manera:

```javascript
export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: [{ find: '@', replacement: path.resolve(import.meta.dirname, 'src') }],
  },
})
```

3. Además, podemos mejorar el autocompletado en nuestro editor de código o IDE creando un archivo `jsconfig.json`. Este archivo le informará al editor sobre nuestra configuración de importación absoluta y nos permitirá importar archivos fácilmente a partir de ella. Crea un nuevo archivo `jsconfig.json`.
4. Dentro de él, añade la siguiente configuración:

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "exclude": ["node_modules"]
}
```

Ahora que podemos usar importaciones absolutas, continuemos definiendo el consumidor.

#### Definición del consumidor (*Defining the consumer*)

Para utilizar un contexto, necesitamos un consumidor. Revisemos primero la forma tradicional de definir un consumidor antes de comenzar a usar Hooks:

1. Edita `src/components/post/Post.jsx` e importa `ThemeContext`:

```javascript
import { ThemeContext } from '@/contexts/ThemeContext.js'
```

2. Envuelve todo el componente con un componente `ThemeContext.Consumer` y una función de renderizado como prop `children`, para hacer uso del valor del contexto:

```jsx
export function Post({ title, content, author }) {
  return (
    <ThemeContext.Consumer>
      {(theme) => (
```

> La función de renderizado (*render prop*) nos permite pasar valores a los hijos de un componente.

3. Dentro de la función de renderizado, ahora podemos usar el valor del contexto para establecer el color del título de una publicación de blog:

```jsx
        <div>
          <h3 style={{ color: theme.primaryColor }}> {title} </h3>
          <div>{content}</div>
          <br />
          <i> Written by <b>{author}</b> </i>
        </div>
      )}
    </ThemeContext.Consumer>
  )
}
```

El uso de contextos de esta manera funciona, pero como aprendimos en el primer capítulo, el uso de componentes con funciones de renderizado sobrecarga el árbol de React y dificulta la depuración y el mantenimiento de nuestra aplicación.

#### Uso de Hooks para consumir un contexto (*Using Hooks to consume a context*)

Una forma mucho mejor de consumir contextos es utilizando un **Hook de Contexto (*Context Hook*)**. De esa manera, podemos usar los valores de contexto como cualquier otro valor.

Sigue estos pasos para cambiar el consumidor a un Hook de Contexto:

1. Edita `src/components/post/Post.jsx` y añade la siguiente importación:

```javascript
import { useContext } from 'react'
```

2. Luego, define un Hook de Contexto de la siguiente manera:

```javascript
export function Post({ title, content, author }) {
  const theme = useContext(ThemeContext)
```

3. A continuación, elimina las partes de envoltorio del consumidor:

```jsx
  return (
    <div>
      <h3 style={{ color: theme.primaryColor }}>{title}</h3>
      <div>{content}</div>
      <br />
      <i> Written by <b>{author}</b> </i>
    </div>
  )
}
```

Como puedes ver, el uso de un Hook de Contexto nos permite consumir directamente el valor del contexto y simplemente renderizar la publicación sin necesidad de un componente envoltorio (*wrapper component*).

4. Inicia la aplicación ejecutando el siguiente comando:

```bash
npm run dev
```

Podemos ver que el título de las publicaciones del blog ahora tiene un color granate (*maroon*). El contexto de tema proporciona correctamente el color para el título de la publicación.

#### Definición del proveedor (*Defining the provider*)

Los contextos utilizan el valor predeterminado pasado a `createContext` cuando no hay ningún proveedor definido. Por ejemplo, imaginemos que nuestro componente utiliza `ThemeContext` y se renderiza así:

```jsx
<Component />
```

En este caso, `primaryColor` se establecerá en `maroon` (como lo definimos antes). Esto puede utilizarse como alternativa (*fallback*), por ejemplo, cuando el componente no está integrado en la aplicación sino en una guía de estilos interactiva (como Storybook).

Cuando hay un proveedor definido, utilizará el valor del proveedor:

```jsx
<ThemeContext.Provider value={{ primaryColor: 'black' }}>
  <Component />
</ThemeContext.Provider>
```

Aquí, `primaryColor` se establecerá en `black`.

Si hay varios proveedores en el árbol, los componentes utilizarán el valor del proveedor padre más cercano:

```jsx
<ThemeContext.Provider value={{ primaryColor: 'black' }}>
  <OtherComponent />
  <ThemeContext.Provider value={{ primaryColor: 'red' }}>
    <Component />
  </ThemeContext.Provider>
</ThemeContext.Provider>
```

En este caso, `primaryColor` en `Component` se establecerá en `red`, ya que es el proveedor más cercano al componente en el árbol. Sin embargo, `OtherComponent` en este ejemplo seguirá teniendo `primaryColor` establecido en `black`.

> Como podemos ver, un contexto sin un proveedor es simplemente un valor estático; el proveedor (especialmente en combinación con otros Hooks, como un Hook de Estado para el valor) es lo que nos permite cambiar dinámicamente el valor de un contexto.

Definamos el proveedor ahora siguiendo estos pasos:

1. Edita `src/App.jsx` e importa `ThemeContext`:

```javascript
import { ThemeContext } from './contexts/ThemeContext.js'
```

2. Luego, envuelve el contenido del componente `App` con el componente `ThemeContext.Provider` y proporciona un valor:

```jsx
export function App() {
  const [posts, dispatch] = useReducer(postsReducer, defaultPosts)
  const [username, setUsername] = useState('')

  return (
    <ThemeContext.Provider value={{ primaryColor: 'black' }}>
      <div style={{ padding: 8 }}>
        <UserBar username={username} setUsername={setUsername} />
        <br />
        {username && <CreatePost username={username} dispatch={dispatch} />}
        <hr />
        <PostList posts={posts} />
      </div>
    </ThemeContext.Provider>
  )
}
```

Ahora, los títulos de las publicaciones vuelven a renderizarse en negro. Si queremos cambiar el valor de nuestro contexto, simplemente podemos ajustar la prop `value` que se pasa al componente proveedor. También podríamos, por ejemplo, usar un Hook de Estado para cambiar dinámicamente el valor de un contexto.

> Si no pasamos una prop `value` al proveedor, ¡no se utilizará el valor predeterminado del contexto! Si definimos un proveedor sin una prop `value`, entonces el valor del contexto será `undefined`.

Ahora que hemos definido un único proveedor para el contexto, pasemos a definir múltiples proveedores anidados.

#### Anidación de proveedores (*Nesting providers*)

Con React Context, también es posible definir múltiples proveedores para el mismo contexto. Mediante esta técnica, podemos anular (*override*) el valor del contexto en ciertas partes de nuestra aplicación. Por ejemplo, supongamos que queremos implementar una sección de publicaciones destacadas (*featured posts*) para nuestro blog:

1. Edita `src/App.jsx` y define un nuevo array `featuredPosts`:

```javascript
const featuredPosts = [
  {
    title: 'React Context',
    content: 'Manage global state with ease!',
    author: 'Daniel Bugl',
  },
]
```

2. Ahora, dentro del componente `App`, renderiza un nuevo componente `PostList` renderizando el array `featuredPosts`, pero envuélvelo dentro de otro `ThemeContext.Provider`:

```jsx
export function App() {
  const [posts, dispatch] = useReducer(postsReducer, defaultPosts)
  const [username, setUsername] = useState('')

  return (
    <ThemeContext.Provider value={{ primaryColor: 'black' }}>
      <div style={{ padding: 8 }}>
        <UserBar username={username} setUsername={setUsername} />
        <br />
        {username && <CreatePost username={username} dispatch={dispatch} />}
        <hr />
        <ThemeContext.Provider value={{ primaryColor: 'salmon' }}>
          <PostList posts={featuredPosts} />
        </ThemeContext.Provider>
        <PostList posts={posts} />
      </div>
    </ThemeContext.Provider>
  )
}
```

Ahora verás que la publicación destacada tiene un color diferente (`salmon`) al de las demás publicaciones.

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter05/Chapter05_1`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Alternativas a los contextos

Debemos tener cuidado de no usar React Context con demasiada frecuencia, ya que dificulta la reutilización de componentes. Solo debemos usar el contexto cuando necesitemos acceder a datos en muchos componentes que se encuentren en diferentes niveles de anidación. Además, debemos asegurarnos de utilizar contextos únicamente para **datos que no cambian con frecuencia**. Los valores de contexto que cambian con mucha frecuencia, especialmente los contextos utilizados en los niveles superiores del árbol de componentes, pueden provocar que grandes partes del árbol se vuelvan a renderizar, provocando problemas de rendimiento. Por eso, para valores que cambian rápidamente, deberíamos utilizar una solución de gestión de estado como Jotai, Redux o MobX. Estas soluciones de gestión de estado nos permiten acceder a pequeñas partes del estado de forma granular y reducir así la cantidad de rerenderizaciones. Los buenos candidatos para los contextos son características como los sistemas de temas (*theming*) y los sistemas de traducción (*i18n*).

Si solo queremos evitar pasar props hacia abajo, en algunos casos podemos pasar el componente renderizado en lugar de los datos. Por ejemplo, supongamos que tenemos un componente `Page` que renderiza un componente `Header`, el cual renderiza un componente `Profile`, que a su vez renderiza un componente `Avatar`. Recibimos una prop `headerSize` en el componente `Page`, la cual necesitamos en el componente `Header`, pero también en el componente `Avatar`:

```jsx
function Page({ headerSize }) {
  return <Header size={headerSize} />
}

function Header({ size }) {
  // ... makes use of size ...
  return <Profile size={size} />
}

function Profile({ size }) {
  // ... does not use size, only passes it down ...
  return <Avatar size={size} />
}

function Avatar({ size }) {
  // ... makes use of size ...
}
```

En lugar de pasar props a través de múltiples niveles, podríamos hacer lo siguiente:

```jsx
function Page({ headerSize }) {
  const profile = (
    <Profile>
      <Avatar size={headerSize} />
    </Profile>
  )
  return <Header size={headerSize} profile={profile} />
}
```

Ahora, solo el componente `Page` necesita conocer las props y no hay necesidad de pasarlas más abajo en el árbol. En este caso, los contextos no son necesarios.

> Este patrón se denomina **inversión de control (*inversion of control*)** y puede hacer que tu código sea mucho más limpio que pasar props continuamente o usar un contexto. Sin embargo, tampoco debemos usar siempre este patrón porque hace que el componente de nivel superior sea más complejo.

Ahora, continuemos aprendiendo sobre el uso del contexto para el estado global.

---

### Uso del contexto para el estado global

Después de aprender a usar React Context para implementar temas en nuestra aplicación de blog, ahora vamos a usar un contexto para evitar tener que pasar manualmente las props `username` y `setUsername`. El estado del usuario es un estado global, lo que significa que se utiliza en toda la aplicación. Además, no cambia con frecuencia. Como tal, es un excelente candidato para usar un contexto. Al igual que antes, comenzamos definiendo el contexto.

#### Definición del contexto (*Defining the context*)

Para definir el contexto, necesitamos usar la función `createContext` nuevamente. En este caso, establecemos el valor predeterminado en un array con una cadena vacía y una función no-op (una función que no hace nada). Más adelante, al definir el proveedor, proporcionaremos este array utilizando el resultado del Hook de Estado. Recuerda que el Hook de Estado devuelve un array como este: `[value, setValue]`.

Comencemos a definir el contexto:

1. Copia la carpeta `Chapter05_1` a una nueva carpeta `Chapter05_2` ejecutando el siguiente comando:

```bash
cp -R Chapter05_1 Chapter05_2
```

2. Abre la nueva carpeta `Chapter05_2` en VS Code.
3. Crea un nuevo archivo `src/contexts/UserContext.js`. Dentro de él, importa la función `createContext`:

```javascript
import { createContext } from 'react'
```

4. Ahora, define el contexto con el valor predeterminado antes mencionado, que imita el valor de retorno de un Hook de Estado, pero con una cadena vacía y una función no-op:

```javascript
export const UserContext = createContext(['', () => {}])
```

Cuando el contexto se consume pero no se ha definido ningún proveedor, devolverá este valor predeterminado.

#### Definición del proveedor de contexto (*Defining the context provider*)

Ya hemos creado un Hook de Estado para el estado `username`. Ahora podemos usar el resultado de este Hook de Estado y pasarlo al proveedor de contexto para que cualquier componente de nuestra aplicación pueda hacer uso de él:

1. Edita `src/App.jsx` e importa `UserContext`:

```javascript
import { UserContext } from './contexts/UserContext.js'
```

2. Luego, envuelve el resultado del componente `App` con `UserContext.Provider`:

```jsx
export function App() {
  const [posts, dispatch] = useReducer(postsReducer, defaultPosts)
  const [username, setUsername] = useState('')

  return (
    <UserContext.Provider value={[username, setUsername]}>
      <ThemeContext.Provider value={{ primaryColor: 'black' }}>
```

3. Ahora, podemos eliminar las props que pasamos anteriormente:

```jsx
        <div style={{ padding: 8 }}>
          <UserBar />
          <br />
          {username && <CreatePost dispatch={dispatch} />}
          <hr />
          <ThemeContext.Provider value={{ primaryColor: 'salmon' }}>
            <PostList posts={featuredPosts} />
          </ThemeContext.Provider>
          <PostList posts={posts} />
        </div>
```

4. No olvides añadir la etiqueta de cierre para `UserContext.Provider`:

```jsx
      </ThemeContext.Provider>
    </UserContext.Provider>
  )
}
```

> Por supuesto, también es posible utilizar un patrón similar para pasar el resultado de un Hook Reducer a un contexto.

El proveedor de contexto ahora proporciona el valor `username` y la función `setUsername` al resto de nuestra aplicación.

#### Refactorización de la aplicación para usar UserContext

Ahora que tenemos un proveedor de contexto, podemos refactorizar el resto de nuestra aplicación para usar el contexto en lugar de props:

1. Primero, edita `src/components/user/UserBar.jsx` y añade importaciones para la función `useContext` y `UserContext`:

```javascript
import { useContext } from 'react'
import { UserContext } from '@/contexts/UserContext.js'
```

2. Luego, elimina las props pasadas al componente:

```javascript
export function UserBar() {
```

3. A continuación, define el Hook de Contexto y obtén el valor `username`:

```javascript
  const [username] = useContext(UserContext)
```

4. Ahora podemos eliminar las props pasadas a los otros componentes:

```jsx
  if (username) {
    return <Logout />
  }
  return (
    <>
      <Login />
      <hr />
      <Register />
    </>
  )
}
```

5. Edita `src/components/user/Login.jsx` y añade la importación de `useContext` y `UserContext`:

```javascript
import { useContext } from 'react'
import { UserContext } from '@/contexts/UserContext.js'
```

6. Luego, elimina las props del componente y añade el Hook de Contexto:

```javascript
export function Login() {
  const [, setUsername] = useContext(UserContext)
```

> Si no necesitamos el primer elemento de un array, podemos omitirlo al desestructurar simplemente colocando una coma sin especificar el nombre de la primera variable.

7. Edita `src/components/user/Logout.jsx` y añade la importación de `useContext` y `UserContext`:

```javascript
import { useState, useEffect, useContext } from 'react'
import { UserContext } from '@/contexts/UserContext.js'
```

8. Luego, elimina las props del componente y añade el Hook de Contexto:

```javascript
export function Logout() {
  const [username, setUsername] = useContext(UserContext)
```

9. Edita `src/components/user/Register.jsx` y añade la importación de `useContext` y `UserContext`:

```javascript
import { useState, useContext } from 'react'
import { UserContext } from '@/contexts/UserContext.js'
```

10. Elimina las props del componente y añade el Hook de Contexto:

```javascript
export function Register() {
  const [, setUsername] = useContext(UserContext)
```

11. Edita `src/components/post/CreatePost.jsx` y añade la importación de `useContext` y `UserContext`:

```javascript
import { useContext } from 'react'
import { UserContext } from '@/contexts/UserContext.js'
```

12. Elimina la prop `username` del componente y añade el Hook de Contexto:

```javascript
export function CreatePost({ dispatch }) {
  const [username] = useContext(UserContext)
```

13. Inicia la aplicación:

```bash
npm run dev
```

Ahora, la aplicación funciona de la misma manera que antes, ¡pero nuestro código es mucho más limpio y conciso gracias a React Context y al Hook de Contexto!

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter05/Chapter05_2`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Resumen

En este capítulo, primero aprendimos sobre React Context como una alternativa a pasar props a través de múltiples niveles de componentes de React. Luego aprendimos sobre los proveedores y consumidores de contexto, y la nueva forma de definir consumidores a través de Hooks. Pusimos en práctica lo aprendido implementando soporte de temas en nuestra aplicación de blog. A continuación, aprendimos cuándo no tiene sentido usar contextos y cuándo deberíamos usar la inversión de control en su lugar. Finalmente, utilizamos un contexto para el estado global de nombre de usuario en nuestra aplicación de blog.

En el próximo capítulo, aprenderemos cómo solicitar datos de un servidor utilizando React y Hooks. Luego, aprenderemos sobre React Suspense para no tener que esperar a que terminen las peticiones antes de renderizar nuestra aplicación.

---

### Preguntas

Para repasar lo aprendido en este capítulo, intenta responder a las siguientes preguntas:

1. ¿Qué problema evitan los contextos?
2. ¿Cuáles son las tres partes de las que constan los contextos?
3. ¿Es obligatorio definir todas las partes para utilizar contextos?
4. ¿Cuál es la ventaja de utilizar Hooks en lugar de los consumidores de contexto tradicionales?
5. ¿Cuál es una alternativa a los contextos y cuándo deberíamos utilizarla?
6. ¿Cómo podemos implementar contextos que cambian dinámicamente?
7. ¿Cuándo tiene sentido utilizar contextos para el estado?

---

### Lecturas complementarias

Si estás interesado en obtener más información sobre los conceptos que hemos aprendido en este capítulo, consulta los siguientes enlaces:

- Documentación oficial sobre React Context: [https://react.dev/learn/passing-data-deeply-with-context](https://react.dev/learn/passing-data-deeply-with-context)
- Lista de códigos de colores HTML (si deseas ajustar tu tema): [https://www.rapidtables.com/web/color/html-color-codes.html](https://www.rapidtables.com/web/color/html-color-codes.html)
- Ejemplo de contextos para estado local utilizado por react-aria: [https://react-spectrum.adobe.com/react-aria/advanced.html#contexts](https://react-spectrum.adobe.com/react-aria/advanced.html#contexts)
- Ejemplo de contextos para estado global utilizado por react-i18next: [https://react.i18next.com/latest/i18nextprovider](https://react.i18next.com/latest/i18nextprovider)
