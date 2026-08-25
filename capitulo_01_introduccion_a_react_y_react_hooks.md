# Parte 1: Introducción a los Hooks

## Capítulo 1: Introducción a React y React Hooks

**React** es una biblioteca de JavaScript utilizada para construir aplicaciones web eficientes y extensibles. React fue desarrollado por Meta y se utiliza en muchas aplicaciones web a gran escala, tales como Facebook, Instagram, Netflix, Shopify, Airbnb, Cloudflare y la BBC.

En este libro, aprenderemos a construir interfaces de usuario complejas y eficientes con React, manteniendo el código simple y extensible. Utilizando el paradigma de los **React Hooks**, podemos simplificar enormemente el manejo del estado y los efectos secundarios en aplicaciones web, garantizando el potencial de crecimiento y extensión de la aplicación a futuro. También aprenderemos sobre **React Context**, **React Suspense** y **Form Actions**, así como la forma en que se pueden utilizar junto con los Hooks. Finalmente, aprenderemos a construir nuestros propios Hooks personalizados y cómo migrar aplicaciones existentes basadas en componentes de clase de React hacia una arquitectura basada en React Hooks.

En este primer capítulo, aprenderemos los principios fundamentales de React y React Hooks. Comenzaremos aprendiendo qué son React y React Hooks, y por qué deberíamos utilizarlos. Luego, pasaremos a conocer el funcionamiento interno de los Hooks. Por último, se presentarán los Hooks proporcionados por React y un par de Hooks proporcionados por la comunidad, tales como Hooks para la obtención de datos (*data fetching*) y enrutamiento (*routing*). Al comprender los fundamentos de React y React Hooks, estaremos mejor preparados para entender los conceptos que se presentarán a lo largo de este libro.

En este capítulo, cubriremos los siguientes temas principales:

- Principios de React
- Motivación para usar React Hooks
- Configuración del entorno de desarrollo
- Primeros pasos con React Hooks

---

### Requisitos técnicos

Debe estar instalada una versión bastante reciente de **Node.js**. El gestor de paquetes de Node (**npm**) también debe estar instalado (debería venir incluido con Node.js). Para obtener más información sobre cómo instalar Node.js, consulta su sitio web oficial: [https://nodejs.org/](https://nodejs.org/).

Utilizaremos **Visual Studio Code (VS Code)** para las guías de este libro, pero todo debería funcionar de manera similar en cualquier otro editor. Para obtener más información sobre cómo instalar VS Code, consulta su sitio web oficial: [https://code.visualstudio.com](https://code.visualstudio.com/).

En este libro, utilizamos las siguientes versiones:

- **Node.js** v22.14.0
- **npm** v10.9.2
- **Visual Studio Code** v1.97.2

Las versiones mencionadas en la lista anterior son las utilizadas en el libro. Aunque instalar una versión más reciente no debería ser un problema, ten en cuenta que ciertos pasos podrían funcionar de manera diferente en una versión más nueva. Si tienes algún problema con el código y los pasos proporcionados en este libro, intenta utilizar las versiones mencionadas.

Puedes encontrar el código de este capítulo en GitHub: [https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter01](https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter01).

Se recomienda encarecidamente que escribas el código por tu cuenta. No te limites a ejecutar simplemente los ejemplos de código proporcionados con el libro. Es importante escribir el código tú mismo para poder aprenderlo y comprenderlo adecuadamente. Sin embargo, si encuentras algún problema, siempre puedes consultar el ejemplo de código.

---

### Principios de React

Antes de comenzar a aprender cómo configurar un proyecto de React, revisemos los tres principios fundamentales de React. Estos principios nos permiten escribir fácilmente aplicaciones web escalables.

React se basa en tres principios fundamentales:

1. **Declarativo (*Declarative*):** En lugar de decirle a React cómo hacer las cosas, le decimos qué es lo que queremos que haga. Por ejemplo, si cambiamos los datos, no necesitamos decirle a React qué componentes deben volver a renderizarse. Eso sería complejo y propenso a errores. En su lugar, simplemente le decimos a React que los datos han cambiado y todos los componentes que utilicen estos datos se actualizarán y volverán a renderizar de manera eficiente por nosotros. React se encarga de los detalles para que podamos concentrarnos en las tareas inmediatas y desarrollar fácilmente nuestra aplicación web.
2. **Basado en componentes (*Component-based*):** React encapsula componentes que gestionan su propio estado y vistas, y luego nos permite componerlos para crear interfaces de usuario complejas.
3. **Aprende una vez, escribe donde sea (*Learn once, write anywhere*):** React no hace suposiciones sobre tu pila tecnológica y busca garantizar que puedas desarrollar aplicaciones sin tener que reescribir el código existente en la medida de lo posible.

Los tres principios fundamentales de React facilitan la escritura de código, la encapsulación de componentes y el uso compartido de código en múltiples plataformas. En lugar de reinventar la rueda, React siempre intenta aprovechar las características existentes de JavaScript tanto como sea posible. Como resultado, aprenderemos sobre patrones de diseño de software que serán aplicables en muchos más casos además del diseño de interfaces de usuario.

Acabamos de aprender que React está basado en componentes. En React, existen dos tipos de componentes:

- **Componentes de función (*Function components*):** Funciones de JavaScript que reciben las *props* como argumento y devuelven la interfaz de usuario (generalmente mediante JSX, que es una extensión de la sintaxis de JavaScript que nos permite escribir marcado similar a HTML directamente dentro del código JavaScript).
- **Componentes de clase (*Class components*):** Clases de JavaScript que proporcionan un método `render`, el cual devuelve la interfaz de usuario (generalmente mediante JSX).

Si bien los componentes de función son más fáciles de definir y comprender, en el pasado se requerían componentes de clase para manejar estado, contextos y muchas más de las características avanzadas de React. Sin embargo, ¡con React Hooks podemos utilizar la mayoría de las características avanzadas de React sin necesidad de un componente de clase!

> Existen ciertas características de React que, al momento de escribir este texto, aún no son posibles con componentes de función y Hooks. Por ejemplo, definir *error boundaries* todavía requiere componentes de clase y los métodos de ciclo de vida `componentDidCatch` y `getDerivedStateFromError`.

---

### Motivación para usar React Hooks

React siempre se esfuerza por hacer que la experiencia del desarrollador sea lo más fluida posible, al tiempo que garantiza mantener un rendimiento adecuado sin que el desarrollador deba preocuparse demasiado por cómo optimizarlo. Sin embargo, a lo largo de los años de uso de React, se han identificado un par de problemas.

Los fragmentos de código de las siguientes subsecciones tienen como objetivo exclusivo brindarte una comprensión de por qué eran necesarios los Hooks, mostrando ejemplos de cómo los desarrolladores abordaban previamente ciertos problemas en React. Si no estás familiarizado con esas formas antiguas, no te preocupes, no es necesario entender las formas antiguas para seguir adelante. En las próximas secciones, aprenderemos a abordar estos problemas de una mejor manera utilizando React Hooks.

Veamos ahora estos problemas en las siguientes subsecciones.

#### Clases confusas (*Confusing classes*)

En el pasado, teníamos que usar componentes de clase con funciones especiales llamadas métodos de ciclo de vida (*life cycle methods*), como `componentDidUpdate`, y métodos especiales para el manejo del estado como `this.setState`, para gestionar los cambios de estado. Las clases de React, y especialmente el contexto de `this`, son difíciles de leer y entender tanto para los desarrolladores como para las máquinas.

`this` es una palabra clave especial en JavaScript que siempre hace referencia al objeto al que pertenece:

- En un método, `this` hace referencia al objeto de la clase (instancia de la clase).
- En un manejador de eventos (*event handler*), `this` hace referencia al elemento que recibió el evento.
- En una función o de manera aislada, `this` hace referencia al objeto global. Por ejemplo, en un navegador, el objeto global es el objeto `Window`.
- En modo estricto (*strict mode*), `this` es `undefined` en una función.
- Además, métodos como `call()` y `apply()` pueden cambiar el objeto al que se refiere `this`, por lo que puede hacer referencia a cualquier objeto.

Para los desarrolladores, las clases resultan difíciles porque `this` siempre hace referencia a cosas distintas, por lo que a veces (por ejemplo, en los manejadores de eventos) necesitamos reasociarlo manualmente (*rebind*) al objeto de la clase. Para las máquinas, las clases son difíciles porque no saben qué métodos de una clase serán llamados ni cómo se modificará `this`, lo que dificulta optimizar el rendimiento y eliminar código no utilizado (*tree shaking*).

Además, las clases a veces nos exigen escribir código en múltiples lugares a la vez. Por ejemplo, si queremos obtener datos cuando el componente se renderiza o cuando cambian las *props* de un componente, necesitamos hacerlo utilizando dos métodos: una vez en `componentDidMount` y otra vez en `componentDidUpdate`.

Para dar un ejemplo, definamos un componente de clase que obtiene datos de una API:

Primero, definimos un componente de clase extendiendo la clase `React.Component`:

```javascript
class ExampleComponent extends React.Component {
```

Luego, definimos el método de ciclo de vida `componentDidMount`, donde extraemos datos de una API:

```javascript
  componentDidMount() {
    fetch(`http://my.api/${this.props.name}`)
      .then(…)
  }
```

Sin embargo, también necesitamos definir el método de ciclo de vida `componentDidUpdate` en caso de que cambie la prop `name`. Además, necesitamos agregar una verificación manual aquí para asegurarnos de volver a solicitar datos solo si la prop `name` cambió, y no cuando cambian otras props:

```javascript
  componentDidUpdate(prevProps) {
    if (this.props.name !== prevProps.name) {
      fetch(`http://my.api/${this.props.name}`)
        .then(...)
    }
  }
}
```

Para hacer el código menos repetitivo, podríamos refactorizar nuestro código creando un método separado llamado `fetchData` y obtener los datos de la siguiente manera:

```javascript
  fetchData() {
    fetch(`http://my.api/${this.props.name}`)
      .then(...)
  }
```

Luego, podríamos llamar al método en `componentDidMount` y `componentDidUpdate`:

```javascript
  componentDidMount() {
    this.fetchData()
  }

  componentDidUpdate(prevProps) {
    if (this.props.name !== prevProps.name) {
      this.fetchData()
    }
  }
```

Sin embargo, aun así necesitamos llamar al método en dos lugares. Cada vez que actualizamos los argumentos pasados al método, debemos actualizarlos en dos lugares, lo que hace que este patrón sea muy propenso a errores y futuros fallos.

#### Infierno de envoltorios (*Wrapper hell*)

Supongamos que ya hemos implementado una función de componente de orden superior (*higher-order component* o HOC) llamada `authenticateUser` que agrega autenticación a uno de nuestros componentes, y un contexto llamado `AuthenticationContext` para proporcionar información sobre el usuario autenticado a través de *render props*. Usaríamos este contexto de la siguiente manera:

Comenzamos importando la función `authenticateUser` para envolver nuestro componente con el contexto, y el componente `AuthenticationContext` para poder acceder al contexto:

```javascript
import authenticateUser, { AuthenticationContext } from './auth'
```

Luego, definimos un componente `App`, donde hacemos uso del componente `AuthenticationContext.Consumer` y de la *render prop* `user`:

```jsx
const App = () => (
  <AuthenticationContext.Consumer>
    {user =>
```

Las *render props* son una forma de pasar props hacia los hijos de un componente. Como podemos ver aquí, la *render prop* nos permitió pasar `user` hacia los componentes hijos de `AuthenticationContext.Consumer`.

Ahora, mostramos diferentes textos dependiendo de si el usuario ha iniciado sesión o no:

```jsx
      user ? `${user} logged in` : 'not logged in'
    }
```

Aquí utilizamos dos conceptos de JavaScript:
- **Operador ternario (*ternary operator*):** Una versión en línea de la condicional `if`. Tiene la siguiente estructura: `ifThisIsTrue ? returnThis : otherwiseReturnThis`.
- **Plantilla de cadena de texto (*template string*):** Se utiliza para insertar variables dentro de una cadena. Se define con comillas invertidas (`` ` ``) en lugar de comillas simples normales (`'`). Las variables se insertan mediante la sintaxis `${variableName}`. También podemos usar cualquier expresión de JavaScript dentro de las llaves `${}` (por ejemplo, `${someValue + 1}`).

Finalmente, exportamos el componente tras envolverlo con el contexto `authenticateUser`, haciendo uso del patrón de componente de orden superior:

```jsx
  </AuthenticationContext.Consumer>
)

export default authenticateUser(App)
```

Los **componentes de orden superior (*Higher-Order Components*)** son funciones que envuelven componentes y les añaden funcionalidad. Eran necesarios para encapsular y reutilizar la lógica de gestión de estado antes de los Hooks.

En este ejemplo, usamos la función HOC `authenticateUser` para agregar la lógica de autenticación a nuestro componente existente. Luego usamos `AuthenticationContext.Consumer` para inyectar la prop `user` en nuestro componente a través de sus *render props*.

Como puedes imaginar, usar muchos componentes de orden superior dará como resultado un árbol enorme con muchos subárboles, un antipatrón llamado **infierno de envoltorios (*wrapper hell*)**. Por ejemplo, cuando queremos usar tres contextos, el infierno de envoltorios luce así:

```jsx
<AuthenticationContext.Consumer>
  {user => (
    <LanguageContext.Consumer>
      {language => (
        <StatusContext.Consumer>
          {status => (
            ...
          )}
        </StatusContext.Consumer>
      )}
    </LanguageContext.Consumer>
  )}
</AuthenticationContext.Consumer>
```

Esto no es fácil de leer ni de escribir, y también es propenso a errores si necesitamos cambiar algo más adelante. El infierno de envoltorios dificulta la depuración porque debemos inspeccionar un árbol de componentes gigantesco en el que muchos componentes solo actúan como simples envoltorios.

Ahora que hemos conocido algunos problemas comunes de React, ¡aprendamos sobre el patrón Hook para abordar estos problemas de una mejor manera!

#### ¡Hooks al rescate! (*Hooks to the rescue!*)

Los React Hooks se basan en los mismos principios fundamentales de React. Encapsulan la gestión del estado utilizando las características existentes de JavaScript. Como resultado, ya no necesitamos aprender y comprender tantas características especializadas de React; simplemente podemos aprovechar nuestro conocimiento existente de JavaScript para usar Hooks.

Usando Hooks, podemos plantear mejores soluciones a los problemas mencionados anteriormente. Los Hooks son simplemente funciones que se pueden llamar dentro de componentes de función. Tampoco necesitamos usar *render props* para los contextos, porque podemos usar sencillamente un Hook de Contexto para obtener los datos que necesitamos. Además, los Hooks nos permiten reutilizar lógica con estado entre componentes sin tener que crear componentes de orden superior.

Por ejemplo, los problemas mencionados anteriormente con los métodos de ciclo de vida se podrían resolver usando un Hook de Efecto (*Effect Hook*), de la siguiente manera:

```javascript
function ExampleComponent({ name }) {
  useEffect(() => {
    fetch(`http://my.api/${name}`)
      .then(...)
  }, [name])
  // ...
}
```

Este Hook de Efecto se activará automáticamente cuando el componente se monte y cada vez que cambie la prop `name`.

El infierno de envoltorios mencionado anteriormente se puede resolver utilizando Hooks de Contexto (*Context Hooks*), de la siguiente forma:

```javascript
const user = useContext(AuthenticationContext)
const language = useContext(LanguageContext)
const status = useContext(StatusContext)
```

Como podemos observar, al utilizar Hooks podemos mantener nuestro código limpio y conciso, asegurando que se mantenga fácil de leer y mantener. Escribir Hooks personalizados también facilita la reutilización de la lógica de la aplicación en un proyecto.

Ahora que sabemos qué problemas pueden resolver los Hooks, podemos comenzar a utilizarlos en la práctica. Sin embargo, primero debemos configurar rápidamente nuestro entorno de desarrollo.

---

### Configuración del entorno de desarrollo

En este libro utilizaremos VS Code como nuestro editor de código. Siéntete libre de utilizar el editor que prefieras, pero ten en cuenta que las extensiones utilizadas y la configuración establecida pueden variar ligeramente según tu elección.

Instalemos ahora VS Code y algunas extensiones útiles, para luego continuar configurando todas las herramientas necesarias para nuestro entorno de desarrollo.

#### Instalación de VS Code y extensiones

Antes de comenzar a desarrollar y configurar las demás herramientas, necesitamos preparar nuestro editor de código siguiendo estos pasos:

1. Descarga VS Code para tu sistema operativo desde el sitio web oficial (al momento de escribir este libro, la URL es [https://code.visualstudio.com/](https://code.visualstudio.com/)). En este libro utilizaremos la versión 1.97.2.
2. Tras descargar e instalar la aplicación, ábrela; deberías ver la ventana principal.
3. Para facilitar las cosas más adelante, instalaremos algunas extensiones; haz clic en el icono de **Extensions** (el quinto icono desde la parte superior izquierda).
4. Se abrirá una barra lateral donde verás **Search Extensions in Marketplace** en la parte superior. Escribe aquí el nombre de la extensión y haz clic en **Install** para instalarla. Comencemos instalando la extensión **ESLint**:
   - **ESLint** (por Microsoft)
   - **Prettier – Code formatter** (por Prettier)
   > El soporte para JavaScript y Node.js ya viene integrado de forma nativa en VS Code.
5. Crea una carpeta para los proyectos realizados en este libro (por ejemplo, puedes llamarla `Learn-React-Hooks-Second-Edition`). Dentro de esta carpeta, crea una nueva carpeta llamada `Chapter01_1`.
6. Abre la carpeta vacía `Chapter01_1` en VS Code.
7. Si aparece un cuadro de diálogo preguntando *Do you trust the authors of the files in this folder?*, marca la casilla *Trust the authors of all files in the parent folder ‘Learn-React-Hooks’* y haz clic en el botón **Yes, I trust the authors**.

Puedes ignorar esta advertencia con total seguridad en tus propios proyectos, ya que tienes la certeza de que no contienen código malicioso. Al abrir carpetas de fuentes no confiables, puedes presionar *No, I don’t trust the authors* y aun así explorar el código; sin embargo, al hacerlo, algunas funciones de VS Code estarán deshabilitadas.

¡Ya hemos configurado VS Code con éxito y estamos listos para comenzar a configurar nuestro proyecto! Si clonaste la carpeta a partir de los ejemplos de código proporcionados en GitHub, también aparecerá una notificación indicando que se encontró un repositorio de Git. Puedes cerrarla simplemente, ya que solo queremos abrir la carpeta `Chapter01_1`.

Ahora que VS Code está listo, continuemos configurando un nuevo proyecto con Vite.

#### Configuración de un proyecto con Vite

Para este libro, utilizaremos Vite para configurar nuestro proyecto, ya que es el servidor de desarrollo local más popular y mejor valorado según la encuesta *The State of JS 2024* ([https://2024.stateofjs.com/](https://2024.stateofjs.com/)).

Vite también facilita la configuración de un proyecto frontend moderno, al tiempo que permite ampliar la configuración más adelante si es necesario. Sigue estos pasos para configurar tu proyecto con Vite:

1. En la barra de menús de VS Code, ve a **Terminal | New Terminal** para abrir una nueva terminal.
2. Dentro de la terminal, ejecuta el siguiente comando:

```bash
npm create vite@6.2.0 .
```

> El símbolo `$` indica que es un comando que debe ingresarse en una terminal. Introduce todo lo que está después del símbolo `$` en la terminal y confirma con Return/Enter para ejecutar el comando. Asegúrate de incluir el punto (`.`) al final del comando para crear el proyecto en la carpeta actual en lugar de crear una subcarpeta nueva.

Para que las instrucciones de este libro sigan funcionando incluso cuando se lancen nuevas versiones, fijamos todos los paquetes a una versión determinada. Por favor, sigue las instrucciones con las versiones indicadas. Al terminar este libro, cuando comiences nuevos proyectos por tu cuenta, siempre debes intentar usar las versiones más recientes, teniendo en cuenta que podrían ser necesarios ajustes para que funcionen. Consulta la documentación de los respectivos paquetes y sigue la ruta de migración desde la versión del libro hasta la versión más reciente.

3. Cuando se te pregunte si se debe instalar `create-vite`, simplemente escribe `y` y presiona la tecla **Return/Enter** para continuar.
4. Si se te pregunta si el directorio actual no está vacío, selecciona la opción **Remove existing files and continue** y presiona **Return/Enter** para confirmarlo.
5. Cuando se te solicite un nombre de paquete (*package name*), confirma la sugerencia predeterminada presionando **Return/Enter**.
6. Cuando se te solicite el framework, usa las teclas de flecha para seleccionar **React** y presiona **Return/Enter**.
7. Cuando se te solicite la variante, selecciona **JavaScript**.

> Por simplicidad y para llegar a una audiencia más amplia, en este libro solo usamos JavaScript puro. Cabe señalar que, hoy en día, TypeScript se utiliza ampliamente en muchos proyectos, por lo que es posible que desees considerar la adopción de TypeScript en tus propios proyectos más adelante. Sin embargo, aprender sobre TypeScript queda fuera del alcance de este libro.

8. Edita el archivo `package.json` y asegúrate de que las versiones de `dependencies` y `devDependencies` sean las siguientes:

```json
"dependencies": {
  "react": "19.0.0",
  "react-dom": "19.0.0"
},
"devDependencies": {
  "@eslint/js": "9.19.0",
  "@types/react": "19.0.8",
  "@types/react-dom": "19.0.3",
  "@vitejs/plugin-react": "4.3.4",
  "eslint": "9.19.0",
  "eslint-plugin-react": "7.37.4",
  "eslint-plugin-react-hooks": "5.0.0",
  "eslint-plugin-react-refresh": "0.4.18",
  "globals": "15.14.0",
  "vite": "6.1.0"
}
```

9. Ahora nuestro proyecto está configurado y podemos ejecutar `npm install` en la terminal para instalar las dependencias.
10. Después, ejecuta `npm run dev` para iniciar el servidor de desarrollo.

> Por simplicidad en la configuración, utilizamos npm directamente. Si prefieres yarn o pnpm, puedes ejecutar `yarn create vite` o `pnpm create vite` respectivamente.

11. En la terminal, verás una URL que te indicará dónde se está ejecutando tu aplicación. Puedes mantener presionada la tecla Ctrl (Cmd en macOS) y hacer clic en el enlace para abrirlo en tu navegador, o ingresar manualmente la URL en un navegador. Abre el enlace en un navegador ahora.
12. Para comprobar si tu aplicación es interactiva, haz clic en el botón con el texto **count is 0**, y debería incrementar el contador cada vez que se presione.

#### Alternativas a Vite

Las alternativas a Vite son empaquetadores (*bundlers*) como webpack, Rollup y Parcel. Estos son altamente configurables, pero a menudo no ofrecen una gran experiencia en servidores de desarrollo. Primero deben empaquetar todo nuestro código antes de servirlo al navegador. En su lugar, Vite admite de forma nativa el estándar ECMAScript Module (ESM). Además, Vite requiere muy poca configuración para comenzar. Una desventaja de Vite es que puede resultar difícil configurar ciertos escenarios más complejos. Un empaquetador prometedor en desarrollo es Rolldown ([https://rolldown.rs](https://rolldown.rs/)); sin embargo, todavía es muy nuevo al momento de escribir este texto.

Ahora que nuestro proyecto base está funcionando, dediquemos un tiempo a configurar herramientas que garanticen las mejores prácticas y un estilo de código coherente.

---

### Configuración de ESLint y Prettier para aplicar buenas prácticas y estilo de código

Ahora que nuestra aplicación React está configurada, configuraremos ESLint para aplicar las mejores prácticas de codificación con JavaScript y React. También configuraremos Prettier para aplicar un estilo de código y formatearlo automáticamente.

#### Instalación de las dependencias necesarias

Primero, instalaremos todas las dependencias necesarias.

1. En la terminal, haz clic en el icono **Split Terminal** en la parte superior derecha del panel de la terminal para crear un nuevo panel de terminal (como alternativa, haz clic derecho en el panel de terminal y selecciona **Split Terminal**). Esto mantendrá nuestra aplicación en ejecución mientras ejecutamos otros comandos.
2. Haz clic en este panel recién abierto para enfocarlo. Luego, ingresa el siguiente comando para instalar Prettier y la configuración de Prettier para ESLint:

```bash
npm install --save-dev --save-exact prettier@3.5.1 eslint-config-prettier@10.0.1
```

> El indicador `--save-dev` en npm guarda esas dependencias como dependencias de desarrollo, lo que significa que solo se instalarán para el desarrollo y no se incluirán en una aplicación desplegada en producción. El indicador `--save-exact` garantiza que las versiones se fijen a la versión exacta proporcionada por el libro.

Una vez instaladas las dependencias, debemos configurar Prettier y ESLint. Comenzaremos configurando Prettier.

#### Configuración de Prettier

Prettier formateará el código por nosotros y reemplazará el formateador de código predeterminado para JavaScript en VS Code. Nos permitirá dedicar más tiempo a escribir código, formateándolo automáticamente de forma correcta al guardar el archivo. Sigue estos pasos para configurar Prettier:

1. Haz clic derecho debajo de la lista de archivos en la barra lateral izquierda de VS Code (si no está abierta, haz clic en el icono de Archivos) y haz clic en **New file...** para crear un nuevo archivo. Nómbralo `.prettierrc.json` (¡no olvides el punto al principio del nombre de archivo!).
2. El archivo recién creado debería abrirse automáticamente; comienza a escribir la siguiente configuración en él. Primero creamos un nuevo objeto y establecemos la opción `trailingComma` en `all` para asegurarnos de que los objetos y arreglos que abarcan varias líneas siempre tengan una coma al final, incluso en el último elemento. Esto reduce la cantidad de líneas modificadas al confirmar un cambio mediante Git:

```json
{
  "trailingComma": "all",
```

3. Luego, establecemos la opción `tabWidth` en dos espacios:

```json
  "tabWidth": 2,
```

4. Establecemos `printWidth` en 80 caracteres por línea para evitar líneas excesivamente largas en nuestro código:

```json
  "printWidth": 80,
```

5. Establecemos la opción `semi` en `false` para evitar puntos y comas donde no sean necesarios:

```json
  "semi": false,
```

6. Finalmente, forzamos el uso de comillas simples en lugar de comillas dobles:

```json
  "jsxSingleQuote": true,
  "singleQuote": true
}
```

> Estas configuraciones para Prettier son solo un ejemplo de una convención de estilo de código. Por supuesto, eres libre de ajustarlas a tus propias preferencias. Hay muchas más opciones, todas las cuales se pueden encontrar en la documentación de Prettier ([https://prettier.io/docs/en/options.html](https://prettier.io/docs/en/options.html)).

#### Configuración de la extensión Prettier

Ahora que tenemos un archivo de configuración para Prettier, debemos asegurarnos de que la extensión de VS Code esté configurada correctamente para formatear el código por nosotros:

1. Abre la configuración de VS Code yendo a **File | Preferences... | Settings** en Windows/Linux, o **Code | Settings... | Settings** en macOS.
2. En el editor de configuración recién abierto, haz clic en la pestaña **Workspace**. Esto garantiza que guardemos todas nuestras configuraciones en un archivo `.vscode/settings.json` en la carpeta de nuestro proyecto. Cuando otros desarrolladores abran nuestro proyecto, también utilizarán automáticamente esas configuraciones.
3. Busca `editor format on save` y marca la casilla de verificación para habilitar el formateo de código al guardar (*format on save*).
4. Busca `editor default formatter` y selecciona **Prettier - Code formatter** de la lista.
5. Para verificar que Prettier funciona, abre el archivo `.prettierrc.json`, agrega algunos espacios adicionales al comienzo de una línea y guarda el archivo. Notarás que Prettier reformateó el código para adherirse al estilo de código definido, reduciendo el número de espacios de indentación a 2.

Ahora que Prettier está configurado correctamente, ya no tenemos que preocuparnos por formatear nuestro código manualmente. ¡Siéntete libre de escribir código sobre la marcha y guardar el archivo para que se formatee automáticamente!

#### Creación de un archivo ignore de Prettier

Para mejorar el rendimiento y evitar ejecutar Prettier en archivos que no deben formatearse automáticamente, podemos ignorar ciertos archivos y carpetas creando un archivo de ignorados de Prettier. Sigue estos pasos:

1. Crea un nuevo archivo llamado `.prettierignore` en la raíz de nuestro proyecto, de forma similar a como creamos el archivo `.prettierrc.json`.
2. Añade el siguiente contenido para ignorar el código fuente transpilado:

```text
dist/
```

> La carpeta `node_modules/` es ignorada automáticamente por Prettier.

Ahora que hemos configurado Prettier con éxito, configuraremos ESLint para aplicar las mejores prácticas de codificación.

#### Configuración de ESLint

Mientras que Prettier se enfoca en el estilo y formateo de nuestro código, ESLint se enfoca en el código en sí, evitando errores comunes o código innecesario. Vamos a configurarlo ahora:

1. Abre el archivo `eslint.config.js` creado automáticamente y añade la siguiente importación:

```javascript
import prettierConfig from 'eslint-config-prettier'
```

2. Desplázate hacia abajo hasta el final del archivo y agrega la configuración de Prettier al final del array, de la siguiente manera:

```javascript
    'react-refresh/only-export-components': [
      'warn',
      { allowConstantExport: true },
    ],
  },
},
prettierConfig,
]
```

3. Además, desactiva la regla `react/prop-types`, de la siguiente manera:

```javascript
    'react-refresh/only-export-components': [
      'warn',
      { allowConstantExport: true },
    ],
    'react/prop-types': 'off',
  },
},
prettierConfig,
]
```

> A partir de React 19, las comprobaciones de tipos de props (`prop-types`) se eliminaron por completo y se ignorarán silenciosamente. La única forma de agregar comprobaciones de tipo a las props es utilizar una solución completa de comprobación de tipos, como TypeScript. Dado que en este libro nos centramos en aprender React puro con Hooks, el uso de TypeScript queda fuera del alcance. Sin embargo, si aún no lo has hecho, te recomiendo encarecidamente aprender TypeScript por tu cuenta después de terminar este libro.

4. Guarda los archivos y ejecuta `npx eslint src` en la terminal para ejecutar el linter. Verás que no hay salida en la consola, lo que significa que todo se analizó correctamente y ¡no hubo errores!

> El comando `npx` nos permite ejecutar comandos proporcionados por paquetes de npm en un contexto similar al que se obtendría ejecutándolos en los scripts de `package.json`. También puede ejecutar paquetes remotos sin instalarlos permanentemente. En caso de que el paquete aún no esté instalado, te preguntará si debe hacerlo.

#### Añadir un nuevo script para ejecutar nuestro linter

En la sección anterior, estuvimos llamando al linter ejecutando `npx eslint src` manualmente. Ahora agregaremos un script de lint a nuestro `package.json`:

1. En la terminal, ejecuta el siguiente comando para definir un script `lint` en nuestro archivo `package.json`:

```bash
npm pkg set scripts.lint="eslint src"
```

2. Ahora, ejecuta `npm run lint` en la terminal. Esto debería ejecutar `eslint src` con éxito, exactamente igual que lo hizo `npx eslint src`.

Ahora que hemos configurado con éxito nuestro entorno de desarrollo, ¡pasemos a aprender sobre el uso de componentes de clase de React frente a React Hooks en la práctica!

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter01/Chapter01_1`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Primeros pasos con React Hooks

Como aprendimos anteriormente en este capítulo, los React Hooks resuelven muchos problemas, especialmente en aplicaciones web de mayor tamaño. Los Hooks se agregaron en React 16.8 y nos permiten usar estado y varias otras características de React sin necesidad de escribir una clase. En esta sección, comenzaremos definiendo un componente de clase y luego escribiremos el mismo componente como un componente de función utilizando Hooks. Después, analizaremos las ventajas de los Hooks y cómo migrar de clases a una solución basada en Hooks.

#### Comenzando con un componente de clase (*Starting with a class component*)

Comencemos creando un componente de clase tradicional de React que nos permita ingresar un nombre y luego mostrarlo en nuestra aplicación:

1. Copia la carpeta `Chapter01_1` a una nueva carpeta `Chapter01_2`, de la siguiente manera:

```bash
cp -R Chapter01_1 Chapter01_2
```

> En macOS, es importante ejecutar el comando con el indicador `-R` en mayúscula, no `-r`. El indicador `-r` maneja los enlaces simbólicos de manera diferente y hace que la carpeta `node_modules/` falle. El indicador `-r` solo existe por razones históricas y no debe usarse en macOS. Prefiere siempre el uso del indicador `-R`.

2. Abre la nueva carpeta `Chapter01_2` en VS Code.
3. Elimina la carpeta `src/assets/` y todo su contenido.
4. Elimina los archivos `src/App.css` y `src/index.css`.
5. Abre el archivo `src/main.jsx` y elimina la siguiente importación:

```javascript
import './index.css'
```

6. Además, ajusta la importación del componente `App` cambiando de una importación por defecto a una importación con nombre (*named import*), de la siguiente manera:

```javascript
import { App } from './App.jsx'
```

> En la mayoría de los casos, es preferible utilizar exportaciones/importaciones con nombre sobre las exportaciones/importaciones por defecto. El uso de exportaciones/importaciones con nombre hace que el código sea menos propenso a errores al refactorizar. Por ejemplo, imagina que tienes un componente `Login`, lo copias y pegas en un nuevo componente `Register`, pero olvidas renombrar internamente el componente a `Register`. Con importaciones por defecto, aún será posible importarlo como `Register`, aunque internamente el componente se llame `Login`. Sin embargo, al depurar en las herramientas de desarrollo de React (*React DevTools*) o al intentar buscar el componente en tu proyecto, lo verás con el nombre `Login`, lo que puede resultar confuso, especialmente en proyectos a gran escala. Al trabajar con funciones, usar exportaciones con nombre es aún más útil porque te permite moverlas fácilmente entre diferentes archivos.

7. Abre el archivo `src/App.jsx` y elimina todo el código existente en él.
8. A continuación, importamos React:

```javascript
import React from 'react'
```

9. Luego comenzamos a definir un componente de clase:

```javascript
export class App extends React.Component {
```

10. A continuación, debemos definir un método constructor donde establecemos el objeto de estado inicial, que será una cadena de texto vacía. Aquí también debemos asegurarnos de llamar a `super(props)` para informarle al constructor de `React.Component` sobre el objeto `props`:

```javascript
  constructor(props) {
    super(props)
    this.state = {
      name: ''
    }
  }
```

11. Ahora definimos un método para establecer la variable `name`, utilizando `this.setState`. Como utilizaremos el método para manejar la entrada de un campo de texto, necesitamos usar `evt.target.value` para obtener el valor del campo de entrada:

```javascript
  handleChange(evt) {
    this.setState({ name: evt.target.value })
  }
```

12. Luego definimos el método `render`, donde mostraremos un campo de entrada y el nombre:

```javascript
  render() {
```

13. Para obtener la variable `name` del objeto `this.state`, utilizaremos la desestructuración (*destructuring*):

```javascript
    const { name } = this.state
```

La instrucción anterior es equivalente a hacer lo siguiente:

```javascript
const name = this.state.name
```

14. Luego, mostramos la variable de estado `name` ingresada actualmente:

```jsx
    return (
      <div>
        <h1>My name is: {name}</h1>
```

15. Mostramos un campo de entrada, pasándole el método manejador:

```jsx
        <input type='text' value={name} onChange={this.handleChange} />
      </div>
    )
  }
}
```

Si ejecutáramos este código ahora, obtendríamos el siguiente error al escribir texto, debido a que pasar el método manejador a `onChange` altera el contexto de `this`:

```text
Uncaught TypeError: Cannot read properties of undefined (reading 'setState')
```

O, en algunos navegadores, podrías recibir el siguiente error en su lugar:

```text
TypeError: undefined is not an object (evaluating 'this.setState')
```

Por lo tanto, ahora debemos ajustar el método constructor y reasociar (*rebind*) el contexto de `this` de nuestro método manejador a la clase. Edita `src/App.jsx` y añade la siguiente línea en el constructor:

```javascript
  constructor(props) {
    super(props)
    this.state = {
      name: ''
    }
    this.handleChange = this.handleChange.bind(this)
  }
```

16. Ejecuta el servidor de desarrollo abriendo una terminal (**Terminal | New Terminal** en VS Code) y ejecutando el siguiente comando:

```bash
npm run dev
```

17. Abre el enlace al servidor de desarrollo en tu navegador y deberías ver el componente renderizado. ¡Prueba a ingresar algo de texto ahora y debería funcionar!

> Como alternativa, desde ES6 es posible usar funciones flecha (*arrow functions*) como métodos de clase para evitar tener que reasociar el contexto de `this`.

¡Finalmente nuestro componente funciona! Como puedes ver, se requiere bastante código repetitivo (*boilerplate*) para lograr que el manejo del estado funcione correctamente con componentes de clase. También tuvimos que reasociar el contexto de `this`; de lo contrario, nuestro método manejador no funcionaría. Esto no es muy intuitivo y es fácil de pasar por alto durante el desarrollo, lo que provoca una experiencia frustrante.

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter01/Chapter01_2`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Uso de Hooks en su lugar (*Using Hooks instead*)

Después de utilizar un componente de clase tradicional para crear nuestra aplicación, vamos a escribir la misma aplicación utilizando Hooks. Al igual que antes, nuestra aplicación nos permitirá ingresar un nombre y luego mostrarlo en la aplicación.

> Solo es posible usar Hooks dentro de componentes de función de React. No puedes usar Hooks en un componente de clase de React.

Sigue estos pasos para comenzar:

1. Copia la carpeta `Chapter01_2` a una nueva carpeta `Chapter01_3`, de la siguiente manera:

```bash
cp -R Chapter01_2 Chapter01_3
```

2. Abre la nueva carpeta `Chapter01_3` en VS Code.
3. Abre el archivo `src/App.jsx` y elimina todo el código existente en él.
4. Primero, importamos el Hook `useState`:

```javascript
import { useState } from 'react'
```

5. Comenzamos con la definición de la función. En nuestro caso, no pasamos ningún argumento porque nuestro componente no tiene props:

```javascript
export function App() {
```

El siguiente paso sería obtener la variable `name` del estado del componente. Sin embargo, no podemos usar `this.state` en componentes de función. Ya hemos aprendido que los Hooks son simplemente funciones de JavaScript, pero ¿qué significa realmente eso? ¡Significa que podemos simplemente usar Hooks desde componentes de función, al igual que cualquier otra función de JavaScript!

Para usar estado a través de Hooks, llamamos a `useState()`, pasando nuestro estado inicial como argumento. Esta función devuelve un array con dos elementos:
- El estado actual
- Una función actualizadora (*setter function*) para modificar el estado

Podemos usar desestructuración para almacenar estos dos elementos en variables separadas, de la siguiente manera:

```javascript
  const [name, setName] = useState('')
```

El código anterior es equivalente a lo siguiente:

```javascript
const nameHook = useState('')
const name = nameHook[0]
const setName = nameHook[1]
```

6. Ahora definimos la función manejadora de entrada, donde hacemos uso de la función actualizadora `setName`:

```javascript
  function handleChange(evt) {
    setName(evt.target.value)
  }
```

Como ahora no estamos tratando con clases, ¡ya no hay necesidad de reasociar `this`!

7. Finalmente, renderizamos nuestra interfaz de usuario devolviéndola desde la función:

```jsx
  return (
    <div>
      <h1>My name is: {name}</h1>
      <input type='text' value={name} onChange={handleChange} />
    </div>
  )
}
```

¡Y eso es todo: hemos utilizado Hooks con éxito por primera vez! Como puedes ver, el Hook `useState` es un reemplazo simple y directo para `this.state` y `this.setState`.

Ejecutemos nuestra aplicación ejecutando `npm run dev` en la terminal y abriendo la URL en un navegador.

Después de implementar la misma aplicación con un componente de clase y un componente de función, comparemos ambas soluciones.

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter01/Chapter01_3`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Comparación de las soluciones (*Comparing the solutions*)

Comparemos nuestras dos soluciones para ver las diferencias entre los componentes de clase y los componentes de función que utilizan Hooks.

#### Componente de clase (*Class component*)

El componente de clase hace uso del método `constructor` para definir el estado y necesita reasociar `this` para poder pasar el método manejador al campo de entrada. El código completo del componente de clase se ve así:

```javascript
import React from 'react'

export class App extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      name: ''
    }
    this.handleChange = this.handleChange.bind(this)
  }

  handleChange(evt) {
    this.setState({ name: evt.target.value })
  }

  render() {
    const { name } = this.state
    return (
      <div>
        <h1>My name is: {name}</h1>
        <input type='text' value={name} onChange={this.handleChange} />
      </div>
    )
  }
}
```

Como podemos ver, el componente de clase necesita una gran cantidad de código repetitivo para inicializar el objeto de estado y las funciones manejadoras.

Ahora, echemos un vistazo al componente de función.

#### Componente de función con un Hook (*Function component with a Hook*)

El componente de función hace uso del Hook `useState` en su lugar, por lo que no necesitamos lidiar con `this` ni con un método constructor. El código completo del componente de función se ve así:

```javascript
import { useState } from 'react'

export function App() {
  const [name, setName] = useState('')

  function handleChange(evt) {
    setName(evt.target.value)
  }

  return (
    <div>
      <h1>My name is: {name}</h1>
      <input type='text' value={name} onChange={handleChange} />
    </div>
  )
}
```

Como podemos ver, los Hooks hacen que nuestro código sea mucho más conciso y fácil de razonar. Ya no tenemos que preocuparnos por cómo funcionan las cosas internamente; ¡podemos usar el estado simplemente accediendo a la función `useState`!

---

### Ventajas de los Hooks (*Advantages of Hooks*)

Recordemos el primer principio de React:

> **Declarativo:** En lugar de decirle a React cómo hacer las cosas, le decimos qué queremos que haga. Como resultado, podemos diseñar fácilmente nuestras aplicaciones y React actualizará y renderizará de manera eficiente los componentes correctos cuando los datos cambien.

Como hemos aprendido en este capítulo, los Hooks nos permiten escribir código que le dice a React qué es lo que queremos. Con los componentes de clase, sin embargo, necesitamos decirle a React cómo hacer las cosas. En consecuencia, los Hooks son mucho más declarativos que los componentes de clase, lo que los convierte en una opción mucho más adecuada dentro del ecosistema de React.

El hecho de que los Hooks sean declarativos también significa que React puede realizar varias optimizaciones en nuestro código, ya que resulta más fácil analizar funciones y llamadas a funciones que clases y su complejo comportamiento con `this`. Además, los Hooks facilitan la abstracción y el uso compartido de lógica común con estado entre componentes. Al usar Hooks, podemos evitar las *render props* y los componentes de orden superior.

Podemos apreciar que los Hooks no solo hacen que nuestro código sea más conciso y fácil de razonar para los desarrolladores, sino que también hacen que el código sea más fácil de optimizar para React.

---

### Migración a Hooks (*Migrating to Hooks*)

Ahora te preguntarás si esto significa que los componentes de clase están obsoletos (*deprecated*) y debemos migrarlo todo a Hooks inmediatamente. Por supuesto que no: los Hooks son totalmente opcionales (*opt-in*). Puedes probar los Hooks en algunos de tus componentes sin necesidad de reescribir el resto del código. El equipo de React tampoco planea eliminar las clases por el momento.

No hay prisa por migrar todo a Hooks ahora mismo. Se recomienda adoptar Hooks gradualmente en aquellos componentes donde resulten más útiles. Por ejemplo, si tienes muchos componentes que manejan una lógica similar, puedes extraer esa lógica a un Hook. También puedes usar componentes de función con Hooks conviviendo lado a lado con componentes de clase.

Los Hooks son compatibles hacia atrás (*backward-compatible*) y proporcionan una API directa para varios conceptos de React que ya conoces: *props*, *state*, *context*, *refs* y el ciclo de vida. Además, los Hooks ofrecen nuevas formas de combinar estos conceptos y encapsular su lógica de una manera mucho más adecuada que no conduce al infierno de envoltorios (*wrapper hell*) ni a problemas similares.

---

### La mentalidad de los Hooks (*The Hooks mindset*)

El objetivo principal de los Hooks es desacoplar la lógica con estado de la lógica de renderizado. Los Hooks nos permiten definir la lógica en funciones separadas y reutilizarlas en múltiples componentes. Con los Hooks, no necesitamos alterar nuestra jerarquía de componentes para implementar lógica con estado. Ya no hay necesidad de definir un componente separado que proporcione la lógica de estado a múltiples componentes: ¡podemos simplemente usar un Hook en su lugar!

Sin embargo, los Hooks requieren una mentalidad completamente diferente respecto al desarrollo tradicional con React. Ya no debemos pensar en el ciclo de vida de los componentes. En su lugar, debemos pensar en el **flujo de datos (*data flow*)**. Por ejemplo, podemos indicarle a los Hooks que se ejecuten cuando cambien ciertas *props* o valores provenientes de otros Hooks. Aprenderemos más sobre este concepto en el [Capítulo 4](https://subscription.packtpub.com/book/web-development/9781836209171/4), *Uso de los Hooks Reducer y Effect*. Tampoco debemos dividir los componentes en función de los métodos de ciclo de vida. En su lugar, podemos utilizar Hooks para gestionar funcionalidades comunes, como la obtención de datos o la configuración de suscripciones de datos.

---

### Reglas de los Hooks (*Rules of Hooks*)

Los Hooks son muy flexibles. Sin embargo, existen ciertas limitaciones al usar Hooks que siempre debemos tener en cuenta:

1. **Los Hooks solo se pueden usar en componentes de función y dentro de otros Hooks**, no en componentes de clase ni en funciones arbitrarias de JavaScript.
2. **El orden de definición de los Hooks importa y debe permanecer inmutable**; por lo tanto, no podemos colocar Hooks dentro de condicionales `if`, bucles (*loops*) o funciones anidadas.

Afortunadamente, Vite ya configuró un plugin de ESLint por nosotros que garantiza que no se violen las reglas de los Hooks.

Analizaremos estas limitaciones con más detalle, así como la forma de trabajar con ellas, a lo largo de este libro.

---

### Resumen

En este primer capítulo del libro, comenzamos aprendiendo sobre los principios fundamentales de React y qué tipos de componentes proporciona. Luego, pasamos a conocer los problemas comunes de los componentes de clase al usar las funciones existentes de React y cómo rompían los principios fundamentales. A continuación, implementamos una aplicación simple utilizando componentes de clase y componentes de función con Hooks, para poder comparar las diferencias entre ambas soluciones. Como descubrimos, los componentes de función con Hooks se adaptan mucho mejor a los principios fundamentales de React: no sufren los mismos problemas que los componentes de clase y hacen que nuestro código sea mucho más conciso y fácil de entender. El equipo de React ahora incluso recomienda usar componentes de función en lugar de componentes de clase, lo que convierte a los componentes de función en la forma estándar y recomendada de escribir código en React. Tras leer este capítulo, los conceptos básicos de React y React Hooks quedan claros. Ahora podemos continuar aprendiendo sobre los Hooks en detalle.

En el próximo capítulo, obtendremos un conocimiento profundo de cómo funciona el Hook de Estado (*State Hook*), reimplementándolo desde cero. Al hacerlo, comprenderemos cómo funcionan los Hooks internamente y cuáles son sus limitaciones. ¡Posteriormente crearemos una pequeña aplicación de blog utilizando el Hook de Estado!

---

### Preguntas

Para repasar lo aprendido en este capítulo, intenta responder a las siguientes preguntas:

1. ¿Cuáles son los tres principios fundamentales de React?
2. ¿Cuáles son los dos tipos de componentes en React?
3. ¿Cuáles son los problemas de los componentes de clase en React?
4. ¿Cuál es el problema al utilizar componentes de orden superior (*higher-order components*) en React?
5. ¿Qué herramienta podemos utilizar para configurar un proyecto de React y cuál es el comando que debemos ejecutar para utilizarla?
6. ¿Qué debemos hacer si obtenemos el siguiente error con componentes de clase: `Uncaught TypeError: Cannot read properties of undefined (reading 'setState')`?
7. ¿Cómo utilizamos el estado de React con Hooks?
8. ¿Cuáles son las ventajas de utilizar componentes de función con Hooks en comparación con componentes de clase?
9. ¿Necesitamos reemplazar todos los componentes de clase con componentes de función usando Hooks al actualizar React?

---

### Lecturas complementarias

Si estás interesado en obtener más información sobre los conceptos que hemos aprendido en este capítulo, consulta los siguientes enlaces:

- Información sobre componentes de función: [https://react.dev/reference/react/Component](https://react.dev/reference/react/Component)
- RFC para React Hooks: [https://github.com/reactjs/rfcs/blob/main/text/0068-react-hooks.md](https://github.com/reactjs/rfcs/blob/main/text/0068-react-hooks.md)
- Desestructuración (*Destructuring*): [https://exploringjs.com/es6/ch_destructuring.html](https://exploringjs.com/es6/ch_destructuring.html)
- Plantillas de cadenas (*Template strings*): [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)
- Operador ternario (*Ternary operator*): [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_operator)
