# Parte 1: Introducción a los Hooks

## Capítulo 2: Uso del Hook de Estado

Tras aprender sobre los principios de React y presentar una introducción a los Hooks, ahora vamos a profundizar en el **Hook de Estado (*State Hook*)**. Comenzaremos aprendiendo cómo funciona internamente el Hook de Estado reimplementándolo nosotros mismos. Hacer esto nos enseñará sobre las limitaciones de los Hooks y por qué existen. Luego, conoceremos posibles APIs alternativas de Hooks y sus problemas asociados. Finalmente, aprenderemos a resolver los problemas comunes derivados de las limitaciones de los Hooks. Al final de este capítulo, sabrás cómo utilizar el Hook de Estado para implementar componentes de función con estado en React.

En este capítulo, cubriremos los siguientes temas principales:

- Reimplementación del Hook de Estado
- Posibles APIs alternativas de Hooks
- Solución de problemas comunes con Hooks

---

### Requisitos técnicos

Debe estar instalada una versión bastante reciente de **Node.js**. El gestor de paquetes de Node (**npm**) también debe estar instalado (debería venir incluido con Node.js). Para obtener más información sobre cómo instalar Node.js, consulta el sitio web oficial: [https://nodejs.org/](https://nodejs.org/).

Utilizaremos **Visual Studio Code (VS Code)** para las guías de este libro, pero todo debería funcionar de manera similar en cualquier otro editor. Para obtener más información sobre cómo instalar VS Code, consulta el sitio web oficial: [https://code.visualstudio.com](https://code.visualstudio.com/).

En este libro, utilizamos las siguientes versiones:

- **Node.js** v22.14.0
- **npm** v10.9.2
- **VS Code** v1.97.2

Aunque instalar una versión más reciente no debería ser un problema, ten en cuenta que ciertos pasos podrían funcionar de manera diferente en una versión más nueva. Si tienes algún problema con el código y los pasos proporcionados en este libro, intenta utilizar las versiones mencionadas.

Puedes encontrar el código de este capítulo en GitHub: [https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter02](https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter02).

Se recomienda encarecidamente que escribas el código por tu cuenta. No te limites a ejecutar simplemente los ejemplos de código proporcionados con el libro. Es importante escribir el código tú mismo para poder aprenderlo y comprenderlo adecuadamente. Sin embargo, si encuentras algún problema, siempre puedes consultar el ejemplo de código.

---

### Reimplementación del Hook de Estado

Para comprender mejor cómo funcionan internamente los Hooks en React, vamos a reimplementar la función `useState` desde cero. Sin embargo, no la implementaremos como un Hook real de React, sino como una función simple de JavaScript, solo para tener una idea de lo que realmente hacen los Hooks.

> Esta reimplementación no es exactamente como funcionan los React Hooks internamente. La implementación real es similar y, por lo tanto, tiene restricciones semejantes. Sin embargo, la implementación real es más extensa de lo que implementaremos aquí.

Comencemos a reimplementar el Hook de Estado:

1. Copia la carpeta `Chapter01_3` a una nueva carpeta `Chapter02_1` ejecutando el siguiente comando:

```bash
cp -R Chapter01_3 Chapter02_1
```

2. Abre la nueva carpeta `Chapter02_1` en VS Code.
3. Primero, necesitamos definir una función para (re)renderizar la aplicación, la cual podemos usar para simular la rerenderización de React cuando cambia el estado del Hook. Si usáramos React Hooks reales, esto se manejaría internamente. Abre `src/main.jsx` y elimina el siguiente código:

```javascript
createRoot(document.getElementById('root')).render(
  <StrictMode>
    <App />
  </StrictMode>,
)
```

Reemplázalo por lo siguiente:

```javascript
const root = createRoot(document.getElementById('root'))

export function renderApp() {
  root.render(
    <StrictMode>
      <App />
    </StrictMode>,
  )
}

renderApp()
```

En este fragmento de código, primero creamos una raíz (*root*) para que nuestra aplicación de React se renderice en ella. Luego, definimos una función para renderizar la aplicación dentro de la raíz. Finalmente, llamamos a la función `renderApp()` para renderizar la aplicación inicialmente.

4. Ahora, abre el archivo `src/App.jsx` y elimina la siguiente línea:

```javascript
import { useState } from 'react'
```

Reemplázala con la siguiente línea:

```javascript
import { renderApp } from './main.jsx'
```

5. Ahora definimos nuestra propia función `useState`. Como ya sabemos, la función `useState` recibe `initialState` como argumento:

```javascript
function useState(initialState) {
```

6. Luego, definimos un valor donde almacenaremos nuestro estado. Al principio, este valor se establecerá en `initialState`:

```javascript
  let value = initialState
```

7. A continuación, definimos la función `setState`, donde estableceremos el nuevo valor y forzaremos la rerenderización de nuestra aplicación:

```javascript
  function setState(nextValue) {
    value = nextValue
    renderApp()
  }
```

8. Finalmente, devolvemos el valor y la función `setState` como un array:

```javascript
  return [value, setState]
}
```

9. Inicia el servidor de desarrollo (mantenlo en ejecución) y luego abre el enlace en tu navegador:

```bash
npm run dev
```

Si intentas ingresar texto en el campo de entrada ahora, notarás que cuando el componente se vuelve a renderizar, el estado se reinicia, por lo que no es posible ingresar ningún texto en el campo. Resolveremos este problema en la siguiente sección.

La razón por la que usamos un array y no un objeto es que normalmente queremos renombrar las variables `value` y `setState`. El uso de un array facilita el renombramiento de las variables mediante la desestructuración. Por ejemplo, si queremos tener un estado para `username`, podríamos hacer lo siguiente:

```javascript
const [username, setUsername] = useState('')
```

Si bien el renombramiento en la desestructuración también es posible con objetos, resultaría más verboso:

```javascript
const { state: username, setState: setUsername } = useState('')
```

Como podemos ver, los Hooks son simples funciones de JavaScript que se encargan de los efectos secundarios, como establecer un valor con estado.

Nuestra función Hook utiliza una **clausura (*closure*)** para almacenar el valor actual. La clausura es un entorno donde las variables existen y se almacenan. En nuestro caso, la función proporciona la clausura y la variable `value` se almacena dentro de esa clausura. La función `setState` también se define dentro de la misma clausura, razón por la cual podemos acceder a la variable `value` dentro de esa función. Fuera de la función `useState`, no podemos acceder directamente a la variable `value` a menos que la devolvamos desde la función.

#### Resolución de problemas con nuestra implementación simple de Hook

El problema de no poder ingresar texto en el campo de entrada se debe a la reinicialización de la variable `value` cada vez que el componente se renderiza, porque llamamos a `useState` cada vez que renderizamos el componente.

En la siguiente sección, resolveremos este problema utilizando una variable global y luego convirtiendo el valor simple en un array, lo que nos permitirá definir múltiples Hooks.

#### Uso de una variable global

Como hemos aprendido, el valor se almacena dentro de la clausura definida por la función `useState`. Cada vez que el componente se vuelve a renderizar, la clausura se reinicializa, lo que significa que la variable `value` se establecerá nuevamente en el `initialState`. Para resolver esto, necesitamos almacenar el valor en una variable global, fuera de la función. De esa manera, la variable `value` estará en la clausura fuera de la función, lo que significa que cuando la función se vuelva a llamar, el valor no se reinicializará.

Podemos definir la variable global de la siguiente manera:

1. Primero, edita `src/App.jsx` y añade la siguiente línea encima de la definición de la función `useState`:

```javascript
let value

function useState(initialState) {
```

2. Luego, elimina la primera línea en la definición de la función:

```javascript
  let value = initialState
```

Reemplázala con el siguiente fragmento de código:

```javascript
  if (value === undefined) {
    value = initialState
  }
```

Intenta ingresar texto en el campo de entrada nuevamente; ¡verás que nuestra función Hook ahora funciona!

Ahora, nuestra función `useState` usa la variable global `value` en lugar de definir la variable `value` dentro de su clausura, por lo que no se reinicializará cuando se vuelva a llamar a la función. Si bien nuestra función Hook actualmente funciona bien, si quisiéramos agregar otro Hook, nos encontraríamos con otro problema: ¡todos los Hooks escribirían en la misma variable global `value`! Echemos un vistazo más de cerca a este problema agregando un segundo Hook a nuestro componente.

#### Definición de múltiples Hooks

Supongamos que queremos crear un segundo campo para el apellido del usuario. Podemos lograrlo siguiendo estos pasos:

1. Edita `src/App.jsx` y comienza definiendo un nuevo Hook al principio del componente `App`, después del Hook actual:

```javascript
export function App() {
  const [name, setName] = useState('')
  const [lastName, setLastName] = useState('')
```

2. Luego, define una función para manejar el cambio de apellido:

```javascript
  function handleLastNameChange(evt) {
    setLastName(evt.target.value)
  }
```

3. A continuación, muestra el valor `lastName` después del primer nombre:

```jsx
  return (
    <div>
      <h1>My name is: {name} {lastName}</h1>
```

4. Finalmente, agrega otro campo de entrada para el apellido:

```jsx
      <input type='text' value={name} onChange={handleChange} />
      <input type='text' value={lastName} onChange={handleLastNameChange} />
```

Intenta ingresar el nombre y el apellido ahora.

Notarás que nuestra función Hook reimplementada usa el mismo valor para ambos estados, por lo que siempre cambiamos ambos campos a la vez. Intentemos solucionar esto ahora.

#### Añadir soporte para múltiples Hooks

Para agregar soporte para múltiples Hooks, necesitamos almacenar un array de valores de Hooks en lugar de una sola variable global. Ahora vamos a refactorizar la variable `value` a un array `values` con los siguientes pasos:

1. Edita `src/App.jsx` y elimina la siguiente línea de código:

```javascript
let value
```

Reemplázala con el siguiente fragmento de código:

```javascript
let values = []
let currentHook = 0
```

2. Luego, edita la primera línea de la función `useState`, donde ahora inicializamos el valor en el índice `currentHook` del array `values`:

```javascript
function useState(initialState) {
  if (values[currentHook] === undefined) {
    values[currentHook] = initialState
  }
```

3. También necesitamos actualizar la función actualizadora (*setter function*) para que solo se actualice el valor de estado correspondiente. Aquí, primero debemos almacenar el valor de `currentHook` en una variable `hookIndex` separada, porque el valor de `currentHook` cambiará más adelante. Esto asegura que se cree una copia de la variable `currentHook` dentro de la clausura de la función `useState`. De lo contrario, la función `useState` accedería a la variable `currentHook` desde la clausura externa, la cual se modifica con cada llamada a `useState`:

```javascript
  let hookIndex = currentHook
  function setState(nextValue) {
    values[hookIndex] = nextValue
    renderApp()
  }
```

4. Edita la instrucción `return` de la función `useState` de la siguiente manera:

```javascript
  const value = values[currentHook++]
  return [value, setState]
}
```

Usando `values[currentHook++]`, pasamos el valor actual de `currentHook` como índice al array `values` y luego incrementamos el valor de `currentHook` en uno. Esto significa que `currentHook` se incrementará después de retornar de la función.

> Si quisiéramos incrementar primero un valor y luego usarlo, podríamos usar la sintaxis `arr[++indexToBeIncremented]`, la cual primero incrementa y luego pasa el resultado al array.

5. Todavía necesitamos reiniciar el contador `currentHook` cuando comenzamos a renderizar nuestro componente. Añade la siguiente línea justo después de la definición del componente:

```javascript
export function App() {
  currentHook = 0
```

Intenta ingresar el nombre y el apellido nuevamente.

¡Finalmente, nuestra sencilla reimplementación del Hook `useState` funciona!

Como podemos ver, el uso de un array global para almacenar los valores de nuestros Hooks resolvió los problemas que teníamos al definir múltiples Hooks.

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter02/Chapter02_1`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

Tras resolver los problemas que teníamos con nuestra implementación personalizada de Hooks, descubramos más sobre las limitaciones de los Hooks en general.

#### ¿Podemos definir Hooks condicionales?

¿Qué pasaría si quisiéramos agregar una casilla de verificación (*checkbox*) que alterne el uso del campo del primer nombre? Averigüémoslo implementando dicha casilla de verificación:

1. Copia la carpeta `Chapter02_1` a una nueva carpeta `Chapter02_2`, de la siguiente manera:

```bash
cp -R Chapter02_1 Chapter02_2
```

2. Abre la nueva carpeta `Chapter02_2` en VS Code.
3. Edita `src/App.jsx` y agrega un nuevo Hook al componente `App`, el cual almacenará el estado de la casilla de verificación:

```javascript
export function App() {
  currentHook = 0
  const [enableFirstName, setEnableFirstName] = useState(false)
```

4. Luego, ajusta el Hook para el estado `name` de modo que solo se use cuando el primer nombre esté habilitado:

```javascript
  // eslint-disable-next-line react-hooks/rules-of-hooks
  const [name, setName] = enableFirstName ? useState('') : ['', () => {}]
  const [lastName, setLastName] = useState('')
```

Debemos desactivar ESLint para esta línea; de lo contrario, nos advertirá indicando que los Hooks no se pueden usar condicionalmente. Para los propósitos de esta demostración, quiero mostrar qué sucede cuando ignoras esta advertencia.

También definimos un valor alternativo a una cadena vacía (`''`) y una función que no hace nada (`() => {}`) cuando el Hook no está definido.

5. A continuación, define una función manejadora para cambiar el estado de la casilla de verificación:

```javascript
  function handleEnableChange(evt) {
    setEnableFirstName(evt.target.checked)
  }
```

6. Finalmente, renderiza la casilla de verificación:

```jsx
  return (
    <div>
      <h1>
        My name is: {name} {lastName}
      </h1>
      <input type='checkbox' value={enableFirstName} onChange={handleEnableChange} />
```

7. Inicia el servidor de desarrollo y luego abre el enlace en tu navegador:

```bash
npm run dev
```

Aquí, usamos el Hook o, si el primer nombre está deshabilitado, devolvemos el estado inicial y una función actualizadora vacía, de modo que editar el campo de entrada no funcione.

Si probamos este código ahora, notaremos que editar el apellido todavía funciona, pero editar el primer nombre no funciona, que es lo que queríamos.

Cuando hacemos clic en la casilla de verificación, sucede algo extraño:
- La casilla de verificación se marca.
- El campo de entrada del primer nombre se habilita.
- El valor del campo del apellido pasa a ser el valor del campo del primer nombre.

Podemos ver que el estado del apellido ahora está en el campo del primer nombre. El valor se intercambia porque **el orden de los Hooks importa**. Como sabemos por nuestra implementación, usamos el índice `currentHook` para averiguar dónde se almacena el estado de cada Hook. Sin embargo, cuando insertamos un Hook adicional entre dos Hooks existentes, el orden se desorganiza.

Antes de marcar la casilla de verificación, el array `values` era el siguiente:

```text
[false, '']
Orden de los Hooks: enableFirstName, lastName
```

Luego, ingresamos texto en el campo `lastName`:

```text
[false, 'Hook']
Orden de los Hooks: enableFirstName, lastName
```

A continuación, hicimos clic en la casilla de verificación, lo que activó otro Hook:

```text
[true, 'Hook', '']
Orden de los Hooks: enableFirstName, name, lastName
```

Como podemos ver, insertar un nuevo Hook entre dos Hooks existentes hace que el Hook `name` se apropie del estado del siguiente Hook (`lastName`) porque ahora tiene el mismo índice que el Hook `lastName` tenía anteriormente. Ahora, el Hook `lastName` no tiene un valor, lo que hace que establezca el valor inicial (una cadena vacía).

Como resultado, alternar la casilla de verificación coloca el valor del campo `lastName` en el campo `name` y deja el campo `lastName` vacío.

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter02/Chapter02_2`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

Tras aprender que los Hooks siempre deben llamarse en el mismo orden, comparemos nuestra implementación personalizada de Hooks con los React Hooks reales.

#### Comparación de nuestra reimplementación con los Hooks reales

Nuestra simple implementación de Hooks ya nos da una idea de cómo funcionan internamente. Sin embargo, en realidad, los Hooks no utilizan variables globales. En su lugar, almacenan el estado dentro del componente de React. También gestionan el contador de Hooks internamente, por lo que no necesitamos restablecer manualmente el conteo en los componentes de función. Además, los Hooks reales activan automáticamente la rerenderización de los componentes cuando cambia el estado. Para poder hacer esto, sin embargo, los Hooks deben llamarse desde un componente de función de React. Los React Hooks no se pueden llamar fuera de React ni dentro de componentes de clase de React.

Al reimplementar el Hook `useState`, hemos aprendido lo siguiente:

- Los Hooks son funciones que acceden a las características de React.
- Los Hooks gestionan efectos secundarios que persisten a través de las rerenderizaciones.
- El orden de definición de los Hooks es crucial.

El punto final es especialmente importante porque significa que no podemos definir Hooks de forma condicional. Siempre debemos ubicar todas las definiciones de Hooks al principio de un componente de función y nunca anidarlas dentro de declaraciones `if`, operadores ternarios o construcciones similares.

Por lo tanto, también hemos aprendido lo siguiente:

- Los React Hooks deben llamarse dentro de componentes de función de React u otros Hooks.
- Los React Hooks no se pueden definir condicionalmente ni en bucles.

Existen algunas limitaciones adicionales de los React Hooks resultantes de las restricciones que hemos conocido:

- Los React Hooks no se pueden definir después de una instrucción `return` condicional.
- Los React Hooks no se pueden definir en manejadores de eventos (*event handlers*).
- Los React Hooks no se pueden definir dentro de bloques `try/catch/finally`.
- Los React Hooks no se pueden definir en las funciones pasadas a `useMemo`, `useReducer` y `useEffect` (aprenderemos más sobre estos tres Hooks a lo largo del libro, pero mantén presente esta limitación por ahora).

Ahora vamos a ver APIs alternativas de Hooks que permitirían Hooks condicionales, pero con sus propias desventajas.

---

### Posibles APIs alternativas de Hooks

A veces sería deseable definir Hooks condicionalmente o en bucles, pero ¿por qué el equipo de React decidió implementar los Hooks de esta manera? ¿Cuáles son las alternativas? Repasemos algunas de ellas para comprender los compromisos (*trade-offs*) que implicó esta decisión.

#### Hooks nombrados (*Named Hooks*)

Podríamos darle un nombre a cada Hook y luego almacenar los Hooks en un objeto en lugar de un array. Sin embargo, esto no generaría una API tan agradable y siempre tendríamos que pensar en nombres únicos para los Hooks:

```javascript
// NOTE: Not the actual React Hook API
const [name, setName] = useState('nameHook', '')
```

Además, surgen preguntas sin resolver: ¿Qué sucedería cuando un condicional se evalúa como `false` o se elimina un elemento de un bucle? ¿Limpiaríamos el estado del Hook? Si no limpiamos el estado del Hook, podríamos estar causando fugas de memoria (*memory leaks*). Si lo limpiamos, podríamos estar descartando involuntariamente la entrada del usuario.

Incluso si resolviéramos estos problemas, todavía existiría el problema de las colisiones de nombres. Si, por ejemplo, creamos un Hook y lo llamamos `nameHook`, ya no podemos llamar a ningún otro Hook `nameHook` en nuestro componente o provocaremos una colisión de nombres. Este es incluso el caso de los nombres de Hooks provenientes de librerías, ¡por lo que también tendríamos que asegurarnos de evitar colisiones con Hooks definidos por bibliotecas externas!

#### Fábricas de Hooks (*Hook factories*)

Alternativamente, podríamos crear una función fábrica de Hooks (*Hook factory function*), la cual use `Symbol` internamente para darle a cada Hook un nombre de clave único:

```javascript
function createUseState() {
  const keyName = Symbol()
  return function useState() {
    // …use unique key name to handle hook state…
  }
}
```

Luego, podríamos usar la función fábrica de la siguiente manera:

```javascript
// NOTE: Not the actual React Hook API
const useNameState = createUseState()

export function App () {
  const [name, setName] = useNameState('')
  // …
}
```

Sin embargo, esto significa que tendríamos que instanciar cada Hook dos veces: una vez fuera del componente y otra vez dentro del componente de función. Esto genera más margen de error. Por ejemplo, si creamos dos Hooks y copiamos y pegamos el código repetitivo, podríamos cometer un error en el nombre de nuestro Hook resultante de la función fábrica, o podríamos cometer un error al usar el Hook dentro del componente.

Este enfoque también hace que sea mucho más difícil crear Hooks personalizados, obligándonos a escribir funciones envoltorias (*wrapper functions*). Además, es más difícil depurar estas funciones envueltas que una función simple.

#### Otras alternativas

Se propusieron muchas APIs alternativas para los React Hooks, pero cada una de ellas sufría problemas similares: hacían que la API fuera más difícil de usar, menos flexible, más compleja de depurar o introducían la posibilidad de colisiones de nombres.

Al final, el equipo de React decidió que la API más simple consistía en realizar un seguimiento de los Hooks contando el orden en que se llamaban. Este enfoque tiene sus propias desventajas, como no poder llamar a los Hooks de forma condicional o en bucles. Sin embargo, facilita enormemente la creación de Hooks personalizados y es sencillo de usar y depurar. Tampoco tenemos que preocuparnos por nombrar Hooks, colisiones de nombres o escribir funciones envoltorias. ¡El enfoque final adoptado nos permite usar Hooks como cualquier otra función!

Ahora que hemos aprendido sobre las diversas propuestas y la implementación final de los Hooks, veamos cómo resolver problemas comunes resultantes de las limitaciones de la API oficial elegida.

---

### Solución de problemas comunes con Hooks

Como descubrimos, implementar Hooks con la API oficial también tiene sus propios compromisos y limitaciones. Ahora vamos a aprender cómo superar estos problemas comunes que surgen de las limitaciones de los React Hooks.

Veremos las soluciones que se pueden utilizar para superar estos dos problemas:
- Solución de Hooks condicionales
- Solución de Hooks en bucles

#### Solución de Hooks condicionales (*Solving conditional Hooks*)

Entonces, ¿cómo implementamos Hooks condicionales? En lugar de hacer que el Hook sea condicional, podemos simplemente definir siempre el Hook y usar su valor cuando lo necesitemos. Si esto no es una opción, necesitamos dividir nuestros componentes, ¡lo cual suele ser mejor práctica de todos modos!

##### Definir siempre el Hook (*Always defining the Hook*)

Para casos simples, como el ejemplo del nombre y apellido que vimos anteriormente, podemos simplemente mantener el Hook definido en todo momento:

1. Copia la carpeta `Chapter02_2` a una nueva carpeta `Chapter02_3`, de la siguiente manera:

```bash
cp -R Chapter02_2 Chapter02_3
```

2. Abre la nueva carpeta `Chapter02_3` en VS Code.
3. Edita `src/App.jsx` y elimina las siguientes dos líneas:

```javascript
// eslint-disable-next-line react-hooks/rules-of-hooks
const [name, setName] = enableFirstName ? useState('') : ['', () => {}]
```

Reemplázalas con lo siguiente:

```javascript
const [name, setName] = useState('')
```

4. Ahora, trasladamos la lógica condicional al lugar donde se renderiza el primer nombre:

```jsx
return (
  <div>
    <h1>
      My name is: {enableFirstName ? name : ''} {lastName}
    </h1>
```

> Si deseas volver a agregar la característica de que el campo del primer nombre ni siquiera se pueda editar cuando no esté habilitado, simplemente agrega el siguiente atributo al campo `<input>`: `disabled={!enableFirstName}`.

5. Ejecuta el servidor de desarrollo y abre el enlace en tu navegador:

```bash
npm run dev
```

¡Ahora nuestro ejemplo funciona correctamente! Definir siempre el Hook suele ser una buena solución para casos simples. En casos más complejos, puede que no sea viable definir siempre el Hook. En ese escenario, necesitaríamos crear un nuevo componente, definir el Hook allí y luego renderizar condicionalmente el componente.

###### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter02/Chapter02_3`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

##### División de componentes (*Splitting up components*)

Otra forma de resolver los Hooks condicionales es dividir un componente en varios componentes y luego renderizar condicionalmente los componentes hijos. Por ejemplo, supongamos que queremos obtener información del usuario desde una base de datos después de que inicia sesión.

No podemos hacer lo siguiente, ya que usar un condicional `if` podría alterar el orden de los Hooks:

```javascript
function UserInfo({ username }) {
  // NOTE: Do NOT do this
  if (username) {
    const info = useFetchUserInfo(username)
    return <div>{info}</div>
  }
  return <div>Not logged in</div>
}
```

En su lugar, debemos crear un componente separado para cuando el usuario haya iniciado sesión, de la siguiente manera:

```javascript
// NOTE: Do this instead
function LoggedInUserInfo({ username }) {
  const info = useFetchUserInfo(username)
  return <div>{info}</div>
}

function UserInfo({ username }) {
  if (username) {
    return <LoggedInUserInfo username={username} />
  }
  return <div>Not logged in</div>
}
```

El uso de dos componentes separados para el estado no autenticado y el autenticado tiene pleno sentido porque nos apegamos al principio de diseño de asignar una única responsabilidad por componente. Por lo general, no poder tener Hooks condicionales no representa una gran limitación si seguimos las mejores prácticas.

#### Solución de Hooks en bucles (*Solving Hooks in loops*)

En ocasiones, puedes encontrarte con un caso en el que desees definir Hooks dentro de bucles; por ejemplo, si tienes una forma de agregar dinámicamente nuevos campos de entrada y necesitas un Hook de Estado para cada uno de ellos.

Para resolver problemas en los que desearíamos tener Hooks en bucles, podemos utilizar un único Hook de Estado que contenga un array o podemos, nuevamente, dividir nuestros componentes. Por ejemplo, supongamos que queremos mostrar todos los usuarios que están conectados (*online*).

##### Uso de un arreglo (*Using an array*)

Podríamos simplemente usar un array que contenga a todos los usuarios, de la siguiente manera:

```javascript
function OnlineUsers({ users }) {
  const [userInfos, setUserInfos] = useState([])

  // ... fetch & keep userInfos up to date ...

  return (
    <div>
      {users.map((username) => {
        const user = userInfos.find((u) => u.username === username)
        return <UserInfo key={username} {...user} />
      })}
    </div>
  )
}
```

Sin embargo, esto podría no tener sentido en todos los casos. Por ejemplo, es posible que no queramos actualizar el estado de los usuarios a través del componente `OnlineUsers` porque tendríamos que seleccionar el estado del usuario correcto del array y luego modificar el array. Esto puede funcionar, pero resulta bastante tedioso.

##### División de componentes (*Splitting up components*)

Una mejor solución consistiría en utilizar el Hook directamente dentro del componente `UserInfo`. De esa manera, podemos mantener actualizado el estado de cada usuario individual sin tener que lidiar con la lógica compleja de arrays:

```javascript
function OnlineUsers({ users }) {
  return (
    <div>
      {users.map((username) => (
        <UserInfo key={username} username={username} />
      ))}
    </div>
  )
}

function UserInfo({ username }) {
  const info = useFetchUserInfo(username)
  // ... keep user info up to date ...
  return <div>{info}</div>
}
```

Como podemos ver, usar un componente para cada funcionalidad mantiene nuestro código simple y conciso, y también evita las limitaciones de los React Hooks.

---

### Resumen

En este capítulo, comenzamos reimplementando la función `useState`, haciendo uso del estado global y las clausuras (*closures*). Luego aprendimos que, para admitir múltiples Hooks, necesitamos realizar un seguimiento de ellos mediante un array. Sin embargo, al usar un array de estados, nos vimos obligados a mantener el orden de los Hooks coherente en todas las llamadas a funciones. Esta limitación hizo imposibles los Hooks condicionales y los Hooks dentro de bucles. A continuación, aprendimos sobre posibles alternativas a la API de Hooks, sus compromisos (*trade-offs*) y por qué se eligió la API final. Por último, aprendimos a resolver los problemas comunes derivados de las limitaciones de los Hooks. Ahora tenemos una sólida comprensión del funcionamiento interno y las restricciones de los Hooks. En el camino, también aprendimos sobre el Hook de Estado en profundidad.

En el próximo capítulo, crearemos una aplicación de blog utilizando el Hook de Estado y aprenderemos a combinar múltiples Hooks.

---

### Preguntas

Para repasar lo aprendido en este capítulo, intenta responder a las siguientes preguntas:

1. ¿Con qué problemas nos encontramos al desarrollar nuestra propia reimplementación del Hook `useState`? ¿Cómo los solucionamos?
2. ¿Por qué no son posibles los Hooks condicionales en la implementación de Hooks de React?
3. ¿Qué debemos tener en cuenta al utilizar Hooks?
4. ¿Cuáles son los problemas comunes de las propuestas de APIs alternativas para los Hooks?
5. ¿Cómo implementamos Hooks condicionales?
6. ¿Cómo implementamos Hooks en bucles?

---

### Lecturas complementarias

Si estás interesado en obtener más información sobre los conceptos que hemos aprendido en este capítulo, consulta los siguientes enlaces:

- Más información sobre los inconvenientes de las APIs alternativas de Hooks: [https://overreacted.io/why-do-hooks-rely-on-call-order/](https://overreacted.io/why-do-hooks-rely-on-call-order/)
- Comentario oficial sobre APIs alternativas de Hooks: [https://github.com/reactjs/rfcs/pull/68#issuecomment-439314884](https://github.com/reactjs/rfcs/pull/68#issuecomment-439314884)
- Documentación oficial sobre las limitaciones y reglas de los Hooks: [https://react.dev/reference/rules/rules-of-hooks](https://react.dev/reference/rules/rules-of-hooks)
- Más información sobre cómo funciona `Symbol`: [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)
