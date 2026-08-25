# Parte 1: Introducción a los Hooks

## Capítulo 3: Creación de tu primera aplicación con React Hooks

Tras aprender en profundidad sobre el Hook de Estado (*State Hook*), ahora vamos a utilizarlo creando una aplicación de blog desde cero. En este capítulo, primero aprenderemos a estructurar aplicaciones React de una manera que escale adecuadamente. Luego, definiremos los componentes que necesitaremos para cubrir las funcionalidades básicas de una aplicación de blog. Finalmente, utilizaremos Hooks para introducir estado en nuestra aplicación. A lo largo de este capítulo, también aprenderemos sobre JSX y diversas características de JavaScript. Al final de este capítulo, tendremos una aplicación de blog básica donde podremos iniciar sesión, registrarnos y crear publicaciones.

En este capítulo, cubriremos los siguientes temas principales:

- Estructuración de proyectos en React
- Implementación de componentes estáticos de React
- Implementación de componentes con estado mediante Hooks

---

### Requisitos técnicos

Debe estar instalada una versión bastante reciente de **Node.js**. El gestor de paquetes de Node (**npm**) también debe estar instalado (debería venir incluido con Node.js). Para obtener más información sobre cómo instalar Node.js, consulta el sitio web oficial: [https://nodejs.org/](https://nodejs.org/).

Utilizaremos **Visual Studio Code (VS Code)** para las guías de este libro, pero todo debería funcionar de manera similar en cualquier otro editor. Para obtener más información sobre cómo instalar VS Code, consulta el sitio web oficial: [https://code.visualstudio.com](https://code.visualstudio.com/).

En este libro, utilizamos las siguientes versiones:

- **Node.js** v22.14.0
- **npm** v10.9.2
- **Visual Studio Code** v1.97.2

Las versiones mencionadas en la lista anterior son las utilizadas en el libro. Aunque instalar una versión más reciente no debería ser un problema, ten en cuenta que ciertos pasos podrían funcionar de manera diferente en una versión más nueva. Si tienes algún problema con el código y los pasos proporcionados en este libro, intenta utilizar las versiones mencionadas.

Puedes encontrar el código de este capítulo en GitHub: [https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter03](https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter03).

Se recomienda encarecidamente que escribas el código por tu cuenta. No te limites a ejecutar simplemente los ejemplos de código proporcionados con el libro. Es importante escribir el código tú mismo para poder aprenderlo y comprenderlo adecuadamente. Sin embargo, si encuentras algún problema, siempre puedes consultar el ejemplo de código.

---

### Estructuración de proyectos en React

Tras aprender sobre los principios de React, cómo usar el Hook de Estado y cómo funcionan los Hooks internamente, ahora utilizaremos el Hook de Estado real para desarrollar una aplicación de blog. En esta sección, estructuraremos las carpetas de una manera que nos permita escalar el proyecto más adelante.

#### Estructura de carpetas (*Folder structure*)

Hay muchas formas en que se pueden estructurar los proyectos, y diferentes estructuras pueden funcionar bien para diferentes proyectos. Por lo general, es una buena idea crear una carpeta `src/` para todo el código fuente, a fin de distinguirlo de los recursos estáticos (*assets*) y los archivos de configuración. Dentro de esta carpeta, una estructura posible es agrupar los archivos por características (*features*). Otra forma popular de estructurar proyectos es agrupar los archivos por rutas (*routes*). Para algunos proyectos, podría tener sentido separar adicionalmente por tipo de archivo, como `src/api/` y `src/components/`. Sin embargo, para nuestro proyecto, nos centraremos principalmente en la interfaz de usuario (UI). Por lo tanto, agruparemos nuestros archivos por funcionalidades dentro de la carpeta `src/`.

> Es una buena idea comenzar con una estructura simple al principio y solo anidar más profundamente cuando realmente lo necesites. No pases demasiado tiempo pensando en la estructura de archivos al iniciar un proyecto porque, por lo general, no sabes de antemano cómo deberían agruparse los archivos y esto puede cambiar más adelante de todos modos. Sin embargo, intenta evitar nombres genéricos para carpetas y archivos, como `utils`, `common` o `shared`. Utiliza un término lo más específico posible y amplíalo a medida que la estructura evolucione.

#### Definición de funcionalidades (*Defining the features*)

Primero tenemos que pensar qué características vamos a implementar en nuestra aplicación de blog. Como mínimo indispensable, queremos implementar las siguientes funcionalidades:

- Registro de usuarios
- Inicio y cierre de sesión (*Login / Logout*)
- Visualización de una sola publicación
- Creación de una nueva publicación
- Listado de publicaciones

#### Propuesta de estructura inicial (*Coming up with an initial structure*)

A partir de las características definidas, podemos abstraer un par de grupos de funcionalidades:

- **Usuario (*User*):** registro, inicio de sesión / cierre de sesión
- **Publicación (*Post*):** creación, visualización, listado

Podríamos simplemente mantenerlo muy simple y crear todos los componentes dentro de la carpeta `src/` sin ninguna anidación. Sin embargo, dado que ya tenemos una idea bastante clara de las características que necesitará una aplicación de blog, ya podemos plantear una estructura de carpetas:

```text
src/
src/user/
src/post/
```

Configuremos la estructura inicial de carpetas ahora:

1. Copia la carpeta `Chapter01_3` a una nueva carpeta `Chapter03_1` ejecutando el siguiente comando:

```bash
cp -R Chapter01_3 Chapter03_1
```

2. Abre la nueva carpeta `Chapter03_1` en VS Code.
3. Dentro de la carpeta `Chapter03_1`, crea las nuevas carpetas `src/user/` y `src/post/`.

#### Estructura de componentes (*Component structure*)

La idea de los componentes en React es hacer que cada componente se encargue de una sola tarea o elemento de la interfaz de usuario. Debemos intentar hacer que los componentes sean lo más modulares posible para poder reutilizar el código. Si nos encontramos copiando y pegando código de un componente a otro, podría ser una buena idea extraer este código común en un componente separado que podamos reutilizar.

Por lo general, al desarrollar software, comenzamos con una maqueta visual (*mock-up*) de la interfaz de usuario.

Al dividir componentes, utilizamos el **principio de responsabilidad única (*Single Responsibility Principle*)**, el cual establece que cada módulo debe tener la responsabilidad sobre una única parte encapsulada de la funcionalidad.

En la maqueta, podemos trazar cajas alrededor de cada componente y subcomponente, y asignarles nombres. Recuerda que cada componente debe tener exactamente una responsabilidad. Comenzamos con los componentes fundamentales que componen esta aplicación:

- Un componente `Logout` para el cierre de sesión (que será reemplazado por los componentes `Login`/`Register` en el estado no autenticado).
- Un componente `CreatePost` para renderizar un formulario que cree nuevas publicaciones.
- Un componente `Post` para las publicaciones individuales.

Una vez identificados los componentes fundamentales, observamos qué componentes pertenecen lógicamente juntos para formar un grupo. Para ello, definimos los componentes contenedores que necesitamos para agrupar los componentes:

- Un componente `PostList`, que se utiliza para agrupar publicaciones.
- Un componente `UserBar` para gestionar el inicio/cierre de sesión y el registro.
- Un componente `App` para agrupar todo lo demás y definir la estructura general de nuestra aplicación.

Ahora que hemos terminado de estructurar nuestro proyecto de React, podemos pasar a implementar los componentes estáticos.

---

### Implementación de componentes estáticos

Antes de comenzar a añadir estado a nuestra aplicación de blog mediante Hooks, modelaremos las características básicas de nuestra aplicación como componentes estáticos de React. Hacer esto significa que primero debemos ocuparnos de la estructura de vista estática de nuestra aplicación.

> Tiene sentido abordar primero la estructura estática, ya que evitará tener que trasladar código dinámico a diferentes componentes más adelante. Además, es más fácil trabajar únicamente con HTML (y CSS) al principio, ayudándonos a comenzar rápidamente con los proyectos. Luego, podemos pasar a implementar el código dinámico y manejar el estado.

Hacer esto paso a paso, en lugar de implementar todo a la vez, nos ayuda a comenzar rápidamente nuevos proyectos sin tener que pensar en demasiadas cosas a la vez, ¡y reduce la cantidad de reestructuraciones que tendremos que hacer más adelante!

#### Implementación de los componentes estáticos relacionados con el usuario

Comenzaremos con la funcionalidad más simple en términos de componentes estáticos: la implementación de la funcionalidad relacionada con el usuario. Necesitaremos cuatro componentes:

- Un componente `Login`, que mostraremos cuando el usuario aún no haya iniciado sesión.
- Un componente `Register`, que también mostraremos cuando el usuario aún no haya iniciado sesión.
- Un componente `Logout`, que se mostrará después de que el usuario haya iniciado sesión.
- Un componente `UserBar`, que mostrará los otros componentes condicionalmente según el estado de autenticación del usuario.

Comenzaremos definiendo los primeros tres componentes, los cuales son componentes independientes. Finalmente, definiremos el componente `UserBar`, que depende de que los otros componentes estén definidos.

##### El componente Login (*The Login component*)

Primero, definiremos el componente `Login`, donde mostraremos dos campos: un campo de nombre de usuario y un campo de contraseña. Además, mostraremos un botón de inicio de sesión:

1. Dentro de la carpeta `Chapter03_1` configurada previamente, crea un nuevo archivo para nuestro componente: `src/user/Login.jsx`.
2. En el archivo `src/user/Login.jsx` recién creado, define un componente que por ahora no acepte ninguna prop:

```jsx
export function Login() {
```

3. Renderiza un `<form>` que evite la acción predeterminada de enviar el formulario y recargar la página:

```jsx
  return (
    <form onSubmit={(e) => e.preventDefault()}>
```

> Aquí estamos utilizando una función anónima (también llamada función flecha) para definir el manejador `onSubmit`. Las funciones anónimas se definen de la siguiente manera:
> - Si no tienen argumentos, podemos escribir `() => { ... }` en lugar de `function () { ... }`.
> - Con argumentos, podemos escribir `(arg1, arg2) => { ... }` en lugar de `function (arg1, arg2) { ... }`.
> - Si no usamos llaves `{ }`, el resultado de la instrucción en el cuerpo de la función también se devolverá automáticamente, lo cual generalmente no es un problema en los manejadores de eventos.

4. Luego, renderiza dos campos para ingresar el nombre de usuario y la contraseña, y un botón para enviar el formulario de inicio de sesión:

```jsx
      <label htmlFor='login-username'>Username: </label>
      <input type='text' name='username' id='login-username' />
      <br />
      <label htmlFor='login-password'>Password: </label>
      <input type='password' name='password' id='login-password' />
      <br />
      <input type='submit' value='Login' />
    </form>
  )
}
```

> El uso de HTML semántico como `<form>` y `<label>` hace que tu aplicación sea más fácil de navegar para las personas que usan software de asistencia de accesibilidad, como lectores de pantalla. Además, al usar HTML semántico, los atajos de teclado (como enviar formularios presionando la tecla Enter/Return) funcionan automáticamente. Usamos los atributos `htmlFor` e `id` para asegurarnos de que los lectores de pantalla sepan a qué campo de entrada pertenece la etiqueta. La prop `id` debe ser única en toda la página, pero para la prop `name`, basta con que sea única dentro del formulario.

El componente estático `Login` ya está implementado, así que vamos a renderizarlo para ver cómo queda.

##### Renderizado del componente Login

Sigue estos pasos para renderizar el componente `Login`:

1. Primero, edita `src/App.jsx` y elimina todo el código existente.
2. Luego, importa el componente `Login`:

```javascript
import { Login } from './user/Login.jsx'
```

3. Define y exporta el componente `App`, que por ahora simplemente renderiza el componente `Login`:

```javascript
export function App() {
  return <Login />
}
```

> Si solo estamos devolviendo un único componente, podemos omitir los paréntesis en la instrucción `return`. En lugar de escribir `return (<Login />)`, podemos simplemente escribir `return <Login />`.

4. Ejecuta el servidor de desarrollo abriendo una terminal (**Terminal | New Terminal** en VS Code) y ejecutando el siguiente comando:

```bash
npm run dev
```

5. Abre el enlace al servidor de desarrollo en tu navegador y deberías ver el componente `Login` renderizado. Si modificas el código, debería actualizarse automáticamente, por lo que puedes mantener el servidor de desarrollo en ejecución a lo largo de este capítulo.

Como podemos ver, el componente estático `Login` se renderiza sin problemas en React.

##### El componente Register (*The Register component*)

El componente estático `Register` será muy similar al componente `Login`, con un campo adicional para repetir la contraseña. A alguien se le podría ocurrir fusionarlos en un solo componente y agregar una prop para alternar el campo adicional; sin embargo, en este caso es mejor que cada componente se encargue de una sola funcionalidad. Más adelante, extenderemos los componentes estáticos con código dinámico; en ese punto, `Register` y `Login` tendrán lógicas sumamente diferentes y tendríamos que separarlos nuevamente.

Comencemos a trabajar en el código del componente `Register`:

1. Crea un nuevo archivo `src/user/Register.jsx`.
2. Define un formulario con los campos de nombre de usuario y contraseña, similar al componente `Login`:

```jsx
export function Register() {
  return (
    <form onSubmit={(e) => e.preventDefault()}>
      <label htmlFor='register-username'>Username: </label>
      <input type='text' name='username' id='register-username' />
      <br />
      <label htmlFor='register-password'>Password: </label>
      <input type='password' name='password' id='register-password' />
      <br />
```

> Ten en cuenta que es preferible utilizar espaciado mediante CSS en lugar de usar la etiqueta HTML `<br />`. Sin embargo, en este libro nos centramos en la estructura de la interfaz de usuario y la integración con Hooks, por lo que usamos HTML simple siempre que sea posible.

3. A continuación, añade el campo para repetir la contraseña:

```jsx
      <label htmlFor='register-password-repeat'>Repeat password: </label>
      <input
        type='password'
        name='password-repeat'
        id='register-password-repeat'
      />
      <br />
```

4. Finalmente, añade un botón de registro:

```jsx
      <input type='submit' value='Register' />
    </form>
  )
}
```

Nuevamente, podemos editar `src/App.jsx` para mostrar nuestro nuevo componente:

```javascript
import { Register } from './user/Register.jsx'

export function App() {
  return <Register />
}
```

Como podemos observar, el componente `Register` se parece mucho al componente `Login`, pero con un campo adicional y un texto diferente en el botón.

##### El componente Logout (*The Logout component*)

A continuación, definiremos el componente `Logout`, el cual mostrará el nombre del usuario actualmente autenticado y un botón para cerrar sesión:

1. Crea un nuevo archivo llamado `src/user/Logout.jsx`.
2. Edita el archivo `src/user/Logout.jsx` y define un componente que reciba la propiedad `username`:

```jsx
export function Logout({ username }) {
```

> Aquí usamos desestructuración para extraer la clave `username` del objeto `props`. React pasa todas las props del componente como primer argumento a una función dentro de un único objeto. Usar la desestructuración en el primer argumento equivale a hacer `const { username } = this.props` en un componente de clase.

3. Dentro de él, devuelve un formulario que muestre el usuario actual y un botón de Logout:

```jsx
  return (
    <form onSubmit={(e) => e.preventDefault()}>
      Logged in as: <b>{username}</b>
      <input type='submit' value='Logout' />
    </form>
  )
}
```

Ahora podemos reemplazar el componente `Register` con el componente `Logout` en `src/App.jsx` para ver nuestro componente recién definido (¡no olvides pasarle la prop `username`!):

```javascript
import { Logout } from './user/Logout.jsx'

export function App() {
  return <Logout username='Daniel Bugl' />
}
```

Una vez definido el componente `Logout`, podemos pasar al componente `UserBar`.

##### El componente UserBar (*The UserBar component*)

Ahora es momento de unir nuestros componentes relacionados con el usuario dentro de un componente `UserBar`, donde mostraremos condicionalmente los componentes `Login` y `Register` o bien el componente `Logout`, dependiendo de si el usuario ya ha iniciado sesión o no.

Comencemos a implementar el componente `UserBar`:

1. Crea un nuevo archivo `src/user/UserBar.jsx`.
2. Dentro de él, importa los componentes `Login`, `Logout` y `Register`:

```javascript
import { Login } from './Login.jsx'
import { Logout } from './Logout.jsx'
import { Register } from './Register.jsx'
```

3. Define el componente `UserBar` y una variable para el nombre de usuario. Por ahora, simplemente le asignamos un valor estático:

```javascript
export function UserBar() {
  const username = ''
```

4. Luego, verificamos si el usuario ha iniciado sesión o no. Si ha iniciado sesión, mostramos el componente `Logout` y le pasamos el `username`:

```javascript
  if (username) {
    return <Logout username={username} />
  }
```

5. De lo contrario, mostramos los componentes `Login` y `Register`. Aquí podemos usar `React.Fragment` (sintaxis abreviada: `<>` y `</>`) en lugar de un contenedor `<div>`. Esto mantiene limpio nuestro árbol de UI, ya que los componentes simplemente se renderizarán uno al lado del otro en lugar de estar envueltos en otro elemento:

```jsx
  return (
    <>
      <Login />
      <hr />
      <Register />
    </>
  )
}
```

6. Edita `src/App.jsx` y muestra el componente `UserBar`:

```javascript
import { UserBar } from './user/UserBar.jsx'

export function App() {
  return <UserBar />
}
```

Como puedes ver, el componente `UserBar` está renderizando correctamente los componentes `Login` y `Register`.

Puedes probar editando la variable estática `username` para ver cómo renderiza el componente `Logout` en su lugar. Edita `src/user/UserBar.jsx` y ajústalo de la siguiente manera:

```javascript
export function UserBar() {
  const username = 'Daniel Bugl'
```

Después de realizar este cambio, el componente `UserBar` renderizará el componente `Logout`.

Más adelante en este capítulo, agregaremos Hooks a nuestra aplicación para que podamos iniciar sesión y hacer que el estado cambie dinámicamente, ¡sin tener que editar el código!

#### Implementación de publicaciones (*Implementing posts*)

Después de implementar todos los componentes relacionados con el usuario, ahora podemos pasar a implementar las publicaciones en nuestra aplicación de blog. Definiremos los siguientes componentes:

- Un componente `Post` para mostrar una sola publicación.
- Un componente `CreatePost` para crear nuevas publicaciones.
- Un componente `PostList` para mostrar una lista de todas las publicaciones.

##### El componente Post (*The Post component*)

Una publicación debe tener un título (*title*), contenido (*content*) y un autor (*author*, el usuario que escribió la publicación).

Implementemos el componente `Post` ahora:

1. Crea un nuevo archivo `src/post/Post.jsx`.
2. Dentro de él, renderiza todas las props siguiendo la maqueta:

```jsx
export function Post({ title, content, author }) {
  return (
    <div>
      <h3>{title}</h3>
      <div>{content}</div>
      <br />
      <i>
        Written by <b>{author}</b>
      </i>
    </div>
  )
}
```

Como siempre, podemos probar nuestro componente editando el archivo `src/App.jsx`:

```jsx
import { Post } from './post/Post.jsx'

export function App() {
  return (
    <Post
      title='React Hooks'
      content='The greatest thing since sliced bread!'
      author='Daniel Bugl'
    />
  )
}
```

Ahora que el componente estático `Post` ha sido implementado, podemos pasar al componente `CreatePost`.

##### El componente CreatePost (*The CreatePost component*)

Necesitamos implementar un formulario para crear una nueva publicación. Aquí le pasamos `username` como prop al componente, ya que el autor siempre debe ser el usuario actualmente conectado. Luego, mostramos el autor y proporcionamos un campo de entrada para el título y un elemento `<textarea>` para el contenido de la publicación del blog.

Implementemos el componente `CreatePost` ahora:

1. Crea un nuevo archivo `src/post/CreatePost.jsx`.
2. Dentro de él, define el componente de acuerdo con la maqueta:

```jsx
export function CreatePost({ username }) {
  return (
    <form onSubmit={(e) => e.preventDefault()}>
      <div>
        Author: <b>{username}</b>
      </div>
      <div>
        <label htmlFor='create-title'>Title:</label>
        <input type='text' name='title' id='create-title' />
      </div>
      <textarea name='content' />
      <input type='submit' value='Create' />
    </form>
  )
}
```

Probamos nuestro componente editando el archivo `src/App.jsx`:

```javascript
import { CreatePost } from './post/CreatePost.jsx'

export function App() {
  return <CreatePost username='Daniel Bugl' />
}
```

El componente `CreatePost` se renderiza correctamente. Ahora podemos pasar al componente `PostList`.

##### El componente PostList (*The PostList component*)

Después de implementar los otros componentes relacionados con las publicaciones, ahora podemos implementar la parte más importante de nuestra aplicación de blog: el feed de publicaciones. Por ahora, el feed simplemente mostrará una lista de publicaciones de blog.

Comencemos a implementar el componente `PostList`:

1. Crea un nuevo archivo `src/post/PostList.jsx`.
2. Primero, importamos `Fragment` y el componente `Post`:

```javascript
import { Fragment } from 'react'
import { Post } from './Post.jsx'
```

3. Luego, definimos el componente de función `PostList`, que acepta un array `posts` como prop. Si `posts` no está definido, lo establecemos en un array vacío por defecto:

```javascript
export function PostList({ posts = [] }) {
```

4. A continuación, renderizamos todas las publicaciones utilizando la función `.map` y la sintaxis de propagación (*spread syntax*):

```jsx
  return (
    <div>
      {posts.map((post, index) => (
        <Post {...post} key={`post-${index}`} />
      ))}
    </div>
  )
}
```

Devolvemos el componente `<Post>` para cada publicación y pasamos todas las claves del objeto `post` al componente como props mediante la sintaxis de propagación, lo cual tiene el mismo efecto que enumerar manualmente todas las claves:

```jsx
<Post title={post.title} author={post.author} content={post.content} />
```

> Si estamos renderizando una lista de elementos, debemos asignarle a cada elemento una prop `key` única. React utiliza esta prop `key` para calcular eficientemente la diferencia entre dos listas cuando los datos han cambiado. La mejor práctica consiste en utilizar un identificador único para la prop `key`, como un ID de base de datos, para que React pueda rastrear los elementos modificados en una lista. En este caso, sin embargo, no tenemos dicho ID, por lo que recurrimos al uso del índice.

> Utilizamos la función `map`, la cual aplica una función a todos los elementos de un array. Esto es similar a usar un bucle `for` y almacenar todos los resultados, pero es más conciso, declarativo y fácil de leer. Alternativamente, podríamos hacer lo siguiente:
>
> ```jsx
> let renderedPosts = []
> let index = 0
> for (let post of posts) {
>   renderedPosts.push(<Post {...post} key={`post-${index}`} />)
>   index++
> }
> return (
>   <div>
>     {renderedPosts}
>   </div>
> )
> ```
> Sin embargo, usar este estilo no se recomienda en React.

En la maqueta visual, tenemos una línea horizontal después de cada publicación. Podemos implementar esto sin un elemento contenedor `<div>` adicional utilizando `Fragment`:

```jsx
      {posts.map((post, index) => (
        <Fragment key={`post-${index}`}>
          <Post {...post} />
          <hr />
        </Fragment>
      ))}
```

> El uso de `Fragment` en lugar de un contenedor `<div>` adicional mantiene limpio el árbol DOM y reduce la cantidad de anidación. La prop `key` siempre debe agregarse al elemento padre superior que se renderiza dentro de la función `map`. En este caso, tuvimos que mover la prop `key` del componente `Post` al `Fragment`.

Probamos nuestro componente editando el archivo `src/App.jsx`:

```jsx
import { PostList } from './post/PostList.jsx'

const posts = [
  {
    title: 'React Hooks',
    content: 'The greatest thing since sliced bread!',
    author: 'Daniel Bugl',
  },
  {
    title: 'Using React Fragments',
    content: 'Keeping the DOM tree clean!',
    author: 'Daniel Bugl',
  },
]

export function App() {
  return <PostList posts={posts} />
}
```

Ahora podemos ver que nuestra aplicación lista todas las publicaciones que definimos en el array `posts`.

##### Integración general de la aplicación (*Putting the app together*)

Después de implementar todos los componentes para reproducir la maqueta visual, solo tenemos que unir todo en el componente `App`:

1. Edita `src/App.jsx` y elimina todo el código actual.
2. Primero, importa los componentes `UserBar`, `CreatePost` y `PostList`:

```javascript
import { UserBar } from './user/UserBar.jsx'
import { CreatePost } from './post/CreatePost.jsx'
import { PostList } from './post/PostList.jsx'
```

3. Luego, define algunos datos simulados (*mock data*) para la aplicación:

```javascript
const username = 'Daniel Bugl'
const posts = [
  {
    title: 'React Hooks',
    content: 'The greatest thing since sliced bread!',
    author: 'Daniel Bugl',
  },
  {
    title: 'Using React Fragments',
    content: 'Keeping the DOM tree clean!',
    author: 'Daniel Bugl',
  },
]
```

4. A continuación, define el componente `App` y devuelve un contenedor con algo de espaciado:

```jsx
export function App() {
  return (
    <div style={{ padding: 8 }}>
```

5. Ahora, renderiza los componentes `UserBar` y `CreatePost`, pasando la prop `username` al componente `CreatePost`:

```jsx
      <UserBar />
      <br />
      <CreatePost username={username} />
```

6. Finalmente, muestra el componente `PostList`, pasándole la prop `posts`:

```jsx
      <hr />
      <PostList posts={posts} />
    </div>
  )
}
```

Tras guardar el archivo, el navegador debería actualizarse automáticamente y ahora podremos ver la interfaz completa con todos los componentes estáticos que definimos anteriormente.

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter03/Chapter03_1`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

Nuestra aplicación ahora se ve exactamente como la maqueta, por lo que podemos pasar a hacer que todos los componentes sean dinámicos utilizando Hooks.

---

### Implementación de componentes con estado mediante Hooks

Ahora que hemos implementado la estructura estática de nuestra aplicación, le agregaremos Hooks de Estado para poder manejar el estado y las interacciones dinámicas.

Primero, creemos una nueva carpeta para la implementación con estado:

1. Copia la carpeta `Chapter03_1` a una nueva carpeta `Chapter03_2`, de la siguiente manera:

```bash
cp -R Chapter03_1 Chapter03_2
```

2. Abre la nueva carpeta `Chapter03_2` en VS Code.

#### Añadir Hooks para las características del usuario

Para agregar Hooks para las funciones de usuario, tendremos que reemplazar la variable estática `username` por un Hook. Luego, necesitamos ajustar el valor cuando iniciamos sesión, nos registramos y cerramos sesión.

##### Ajustar UserBar (*Adjusting UserBar*)

Cuando creamos el componente `UserBar`, definimos estáticamente una variable `username`. ¡Ahora vamos a reemplazarla con un Hook de Estado!

Modifiquemos el componente `UserBar` para hacerlo dinámico:

1. Edita `src/user/UserBar.jsx` e importa el Hook `useState`:

```javascript
import { useState } from 'react'
```

2. Elimina la siguiente línea de código:

```javascript
const username = 'Daniel Bugl'
```

Reemplázala con un Hook de Estado, usando un nombre de usuario vacío como valor predeterminado:

```javascript
const [username, setUsername] = useState('')
```

3. Luego, pasa la función `setUsername` al componente `Logout`:

```jsx
  if (username) {
    return <Logout username={username} setUsername={setUsername} />
  }
```

> Por simplicidad y para que sea más fácil seguir dónde se maneja el estado, pasamos el `username` y la función `setUsername` del Hook de Estado directamente a los otros componentes. En proyectos del mundo real, sería mejor usar nombres específicos para los manejadores (como `onLogout`). Esto reduce el acoplamiento entre componentes.

4. Además, pasa la función `setUsername` a los componentes `Login` y `Register` respectivamente:

```jsx
  return (
    <>
      <Login setUsername={setUsername} />
      <hr />
      <Register setUsername={setUsername} />
    </>
  )
}
```

Ahora, el componente `UserBar` se encarga de configurar el nombre de usuario de forma dinámica. Sin embargo, todavía necesitamos modificar los otros componentes para agregar los manejadores.

5. Edita `src/user/Logout.jsx` y define una función `handleSubmit`:

```jsx
export function Logout({ username, setUsername }) {
  function handleSubmit(e) {
    e.preventDefault()
    setUsername('')
  }
```

> En React 19, se introdujeron las **Form Actions** como una forma avanzada de manejar el envío de formularios. Aprenderemos más sobre Form Actions en el [Capítulo 7](https://subscription.packtpub.com/book/web-development/9781836209171/7), *Uso de Hooks para el manejo de formularios*. En este capítulo, nos centraremos en el uso del Hook de Estado y la forma tradicional de manejar formularios mediante una función manejadora `onSubmit`.

6. Luego, reemplaza el manejador `onSubmit` existente con la función recién definida:

```jsx
  return (
    <form onSubmit={handleSubmit}>
```

7. Edita `src/user/Login.jsx` y define una función `handleSubmit`:

```jsx
export function Login({ setUsername }) {
  function handleSubmit(e) {
    e.preventDefault()
    const username = e.target.elements.username.value
    setUsername(username)
  }
  return (
    <form onSubmit={handleSubmit}>
```

> Como podemos ver, podemos acceder directamente al valor del campo `username` desde el formulario usando `e.target.elements`. La clave del elemento del formulario es equivalente a la prop `name` en el elemento `<input>`.

8. Edita `src/user/Register.jsx` y define una función `handleSubmit`:

```jsx
export function Register({ setUsername }) {
  function handleSubmit(e) {
    e.preventDefault()
    const username = e.target.elements.username.value
    setUsername(username)
  }
  return (
    <form onSubmit={handleSubmit}>
```

Ahora puedes probar a registrarte, iniciar sesión y cerrar sesión, y ver cómo cambia el estado en todos los componentes.

##### Añadir validación (*Adding validation*)

Al probar las funciones de inicio de sesión y registro, es posible que hayas notado que no hay ninguna validación. Para una validación simple (como campos obligatorios), podemos usar directamente las funciones de HTML. La validación de HTML evitará que el usuario envíe el formulario si un campo no es válido y mostrará una ventana emergente indicando qué está mal. Sin embargo, para una validación más compleja, como verificar si la contraseña repetida coincide, necesitaremos usar un Hook de Estado para realizar un seguimiento del estado de error del formulario.

Comencemos a implementar la validación:

1. Edita `src/user/Login.jsx` y agrega la prop `required` a los campos de entrada:

```jsx
      <input type='text' name='username' id='login-username' required />
      …
      <input type='password' name='password' id='login-password' required />
```

2. Edita `src/user/Register.jsx` y agrega la prop `required` también:

```jsx
      <input type='text' name='username' id='register-username' required />
      …
      <input type='password' name='password' id='register-password' required />
      …
      <input type='password' name='password-repeat' id='register-password-repeat' required />
```

3. En el archivo `src/user/Register.jsx`, importa también la función `useState`:

```javascript
import { useState } from 'react'
```

4. Luego, agrega un nuevo Hook de Estado para realizar el seguimiento del estado de error:

```javascript
export function Register({ setUsername }) {
  const [invalidRepeat, setInvalidRepeat] = useState(false)
```

> Este tipo de estado se denomina **estado local (*local state*)**, ya que solo se necesita dentro de un único componente.

5. En la función `handleSubmit`, verifica si los campos de contraseña y repetición de contraseña coinciden. Si no es así, establece el estado de error y retorna de la función:

```javascript
  function handleSubmit(e) {
    e.preventDefault()
    if (
      e.target.elements.password.value !==
      e.target.elements['password-repeat'].value
    ) {
      setInvalidRepeat(true)
      return
    }
```

> Los retornos tempranos (*early returns*) de funciones cuando no se cumple una determinada condición suelen ser preferibles a anidar cláusulas `if`. Retornar temprano mantiene la función fácil de leer y evita problemas donde el código se ejecute accidentalmente.

6. Después de la cláusula `if`, si las contraseñas coinciden, restablece el estado de error y procesa el registro:

```javascript
    setInvalidRepeat(false)
    const username = e.target.elements.username.value
    setUsername(username)
  }
```

7. Al final del formulario, antes del botón Register, insertamos un mensaje de error si el estado de error se activó:

```jsx
      <br />
      {invalidRepeat && (
        <div style={{ color: 'red' }}>Passwords must match.</div>
      )}
      <input type='submit' value='Register' />
    </form>
```

Si intentamos registrarnos ahora sin repetir adecuadamente la contraseña, veremos el mensaje de error correspondiente.

##### Pasar el usuario a CreatePost (*Passing the user to CreatePost*)

El componente `CreatePost` todavía utiliza el nombre de usuario codificado de forma rígida (*hardcoded*). Para poder acceder al nombre de usuario allí, debemos mover el Hook del componente `UserBar` hacia el componente `App`:

1. Edita `src/user/UserBar.jsx` y corta/elimina la siguiente definición de Hook:

```javascript
export function UserBar() {
  const [username, setUsername] = useState('')
```

2. Luego, ajusta la definición de la función para aceptar estos dos elementos como props:

```javascript
export function UserBar({ username, setUsername }) {
```

3. Elimina la siguiente importación de `useState` en `src/user/UserBar.jsx`:

```javascript
import { useState } from 'react'
```

4. Ahora, edita `src/App.jsx` e importa la función `useState` allí:

```javascript
import { useState } from 'react'
```

5. Elimina la siguiente línea de código:

```javascript
const username = 'Daniel Bugl'
```

6. Dentro del componente de función `App`, agrega el Hook que eliminamos anteriormente:

```javascript
export function App() {
  const [username, setUsername] = useState('')
```

> Este tipo de estado se llama **estado global (*global state*)**, ya que se necesita en múltiples componentes en toda la aplicación de blog, razón por la cual trasladamos el Hook de Estado al componente `App`.

7. Luego, pasa el valor `username` y la función `setUsername` al componente `UserBar`:

```jsx
  return (
    <div style={{ padding: 8 }}>
      <UserBar username={username} setUsername={setUsername} />
```

> En el [Capítulo 5](https://subscription.packtpub.com/book/web-development/9781836209171/5), *Implementación de contextos de React*, aprenderemos una solución mejor para proporcionar el estado de autenticación a otros componentes. Por ahora, simplemente transmitiremos el valor y la función a través de props.

8. Finalmente, asegúrate de que el componente `CreatePost` solo se renderice cuando el usuario haya iniciado sesión (`username` esté definido):

```jsx
      <br />
      {username && <CreatePost username={username} />}
```

¡Ahora que las funciones de usuario están completamente implementadas, podemos pasar a usar Hooks para implementar las características de publicaciones!

#### Añadir Hooks para las características de publicaciones

Tras implementar las funciones de usuario, implementaremos la creación dinámica de publicaciones. Lo haremos primero ajustando el componente `App` y luego modificando el componente `CreatePost` para poder insertar nuevas publicaciones.

##### Ajustar el componente App (*Adjusting the App component*)

De manera similar al estado `username`, definiremos `posts` como un estado global en el componente `App` y lo proporcionaremos a otros componentes desde allí.

Comencemos ajustando el componente `App`:

1. Edita `src/App.jsx` y renombra el array `posts` actual a `defaultPosts`:

```javascript
const defaultPosts = [
  {
    title: 'React Hooks',
    content: 'The greatest thing since sliced bread!',
    author: 'Daniel Bugl',
  },
  {
    title: 'Using React Fragments',
    content: 'Keeping the DOM tree clean!',
    author: 'Daniel Bugl',
  },
]
```

2. Luego, define un nuevo Hook para el estado `posts` dentro de la función `App`:

```javascript
export function App() {
  const [posts, setPosts] = useState(defaultPosts)
```

3. Ahora, pasa `setPosts` como una prop al componente `CreatePost`:

```jsx
      {username && (
        <CreatePost username={username} setPosts={setPosts} />
      )}
```

##### Ajustar el componente CreatePost (*Adjusting the CreatePost component*)

Ahora necesitamos usar la función `setPosts` para insertar una nueva publicación cuando se presiona el botón Create:

1. Edita `src/post/CreatePost.jsx` y ajusta la definición de la función para aceptar la prop `setPosts`:

```javascript
export function CreatePost({ username, setPosts }) {
```

2. A continuación, define una función `handleSubmit`, en la que primero reunimos todos los valores que necesitamos:

```javascript
  function handleSubmit(e) {
    e.preventDefault()
    const form = e.target
    const title = form.elements.title.value
    const content = form.elements.content.value
    const newPost = { title, content, author: username }
```

> Aquí acortamos la asignación de objeto `{ title: title }` a `{ title }`, lo que produce el mismo efecto.

3. Luego, insertamos la nueva publicación en el array:

```javascript
    setPosts((posts) => [newPost, ...posts])
```

> Aquí estamos utilizando una función para obtener el valor actual del Hook de Estado, y luego devolvemos un nuevo valor con la nueva publicación insertada al principio del array.

4. Finalmente, reiniciamos el formulario para limpiar todos los campos de entrada:

```javascript
    form.reset()
  }
```

5. Todavía necesitamos asignar la función recién definida al manejador `onSubmit`:

```jsx
  return (
    <form onSubmit={handleSubmit}>
```

¡Ahora podemos iniciar sesión y crear una nueva publicación, y se insertará al principio del feed!

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter03/Chapter03_2`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Resumen

En este capítulo, ¡desarrollamos nuestra propia aplicación de blog desde cero! Comenzamos con una maqueta visual y luego creamos componentes estáticos para representarla. Después, implementamos Hooks para permitir un comportamiento dinámico. A lo largo del capítulo, aprendimos cómo gestionar el estado local y global utilizando Hooks. Además, aprendimos a usar múltiples Hooks y en qué componentes definirlos y almacenar el estado. También aprendimos cómo resolver casos de uso comunes, como la validación y el envío de formularios.

En el próximo capítulo, [Capítulo 4](https://subscription.packtpub.com/book/web-development/9781836209171/4), *Uso de los Hooks Reducer y Effect*, aprenderemos sobre el Hook Reducer, que nos permite gestionar ciertos cambios de estado más fácilmente. Además, aprenderemos sobre el Hook de Efecto, el cual nos permite ejecutar código con efectos secundarios.

---

### Preguntas

Para repasar lo aprendido en este capítulo, intenta responder a las siguientes preguntas:

1. ¿Cuáles son buenas formas de estructurar carpetas en React?
2. ¿Qué principio deberíamos utilizar al dividir los componentes de React?
3. ¿Qué hace la función `map`?
4. ¿Cómo funciona la desestructuración (*destructuring*) y cuándo la usamos?
5. ¿Cómo funciona el operador de propagación (*spread operator*) y cuándo lo usamos?
6. ¿Cómo gestionamos la validación y el envío de formularios?
7. ¿Dónde deben definirse los Hooks de Estado locales?
8. ¿Qué es el estado global?
9. ¿Dónde deben definirse los Hooks de Estado globales?

---

### Lecturas complementarias

Si estás interesado en obtener más información sobre los conceptos que hemos aprendido en este capítulo, consulta el siguiente enlace:

- Documentación oficial sobre *Pensar en React* (*Thinking in React*): [https://react.dev/learn/thinking-in-react](https://react.dev/learn/thinking-in-react)
