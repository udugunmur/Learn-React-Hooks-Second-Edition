# Parte 3: Refactorización y migración de código existente

## Capítulo 11: Reglas de los Hooks

En el capítulo anterior, aprendimos sobre el uso de diversos Hooks desarrollados por la comunidad de React, así como dónde encontrar más de ellos.

En este capítulo, aprenderemos todo lo que hay que saber y tener en cuenta al usar Hooks. También aprenderemos lo que necesitamos saber para comenzar a desarrollar nuestros propios Hooks personalizados. Los Hooks tienen ciertas limitaciones con respecto a los lugares y el orden en que se definen. Violar las reglas de los Hooks puede causar errores (*bugs*) o comportamientos inesperados, por lo que debemos asegurarnos de aprender y aplicar estas reglas.

En este capítulo, cubriremos los siguientes temas principales:

- Uso de Hooks
- Orden de los Hooks
- Nombres de los Hooks
- Aplicación de las reglas de los Hooks

---

### Uso de Hooks

Los Hooks **solo se pueden usar en**:

- **Componentes funcionales de React**
- **Hooks personalizados** (aprenderemos sobre la creación de Hooks personalizados en el próximo capítulo)

Los Hooks **NO se pueden usar**:

- Dentro de **condiciones** (`if`, `switch`) o **bucles** (`for`, `while`)
- Después de una sentencia de **retorno condicional** (`return`)
- En **manejadores de eventos** (*event handlers*)
- En **componentes de clase**
- Dentro de funciones pasadas a Hooks de **Memo**, **Reducer** o **Effect**
- Dentro de bloques `try`/`catch`/`finally`

> En algunos lugares, como en la documentación oficial de React, usar un Hook se denomina a veces "llamar al Hook" (*calling the Hook*).

Los Hooks son funciones normales de JavaScript, excepto que React depende de que se invoquen desde el interior de un componente funcional. Por supuesto, los Hooks personalizados que utilizan otros Hooks se pueden definir fuera de los componentes funcionales de React, pero al utilizarlos, siempre debemos asegurarnos de llamarlos dentro de un componente de React o dentro de otro Hook personalizado.

A continuación, aprenderemos sobre las reglas relativas al orden de los Hooks.

---

### Orden de los Hooks

Utiliza los Hooks **únicamente en el nivel superior** (no anidados), idealmente al principio de los componentes funcionales o Hooks personalizados, de la siguiente manera:

```javascript
function ExampleComponent() {
  const [name, setName] = useState('')
  // …
}

function useCustomHook() {
  const [name, setName] = useState('')
  return { name, setName }
}
```

No utilices Hooks dentro de condiciones, bucles o funciones anidadas; hacerlo cambia el orden de ejecución de los Hooks, lo que genera errores. Ya aprendimos que cambiar el orden de los Hooks provoca que el estado se mezcle entre múltiples Hooks.

Para recapitular, en el Capítulo 2 (*Uso del Hook de Estado*, Ejemplo 2), aprendimos que no podemos hacer lo siguiente:

```javascript
const [enableFirstName, setEnableFirstName] = useState(false)
const [name, setName] = enableFirstName ? useState('') : ['', () => {}]
const [lastName, setLastName] = useState('')
```

En ese ejemplo, renderizamos una casilla de verificación y dos campos de entrada para el nombre y el apellido. En ese momento, el orden de los Hooks era el siguiente:

1. `enableFirstName`
2. `lastName`

Luego, al hacer clic en la casilla de verificación para habilitar el campo del nombre, el orden de los Hooks cambió, quedando de la siguiente forma:

1. `enableFirstName`
2. `firstName`
3. `lastName`

Dado que React confía únicamente en el **orden de llamada de los Hooks** para gestionar su estado interno, el campo `firstName` pasó a ser el segundo Hook y recibió erróneamente el estado que pertenecía a `lastName`.

Si utilizamos el Hook `useState` real de React en este escenario, podemos ver que React detecta automáticamente cuándo ha cambiado el orden de los Hooks y registra una advertencia (*warning*). Al ejecutar React en modo de desarrollo, además lanzará un error y detendrá la ejecución de la aplicación.

Cambiar el orden de los Hooks o habilitar Hooks condicionalmente no es posible, ya que React utiliza internamente el orden de llamada para rastrear qué datos pertenecen a cada Hook.

Para solucionar este problema, siempre definimos el Hook de forma incondicional:

```javascript
const [enableFirstName, setEnableFirstName] = useState(false)
const [name, setName] = useState('')
const [lastName, setLastName] = useState('')
```

Y luego renderizamos condicionalmente el valor en el JSX, en lugar de condicionar la definición del Hook:

```jsx
My name is: {enableFirstName ? name : ''} {lastName}
```

---

### Nombres de los Hooks

En cuanto a la nomenclatura de los Hooks, existe la convención estricta de que las funciones de Hook siempre deben tener el prefijo **`use`**, seguido del nombre del Hook comenzando con una letra mayúscula (*camelCase*). Por ejemplo: `useState`, `useEffect` y `useQuery`.

Esto es fundamental porque, de lo contrario, las herramientas y los desarrolladores no sabrían qué funciones son Hooks y cuáles no. Especialmente al aplicar de forma automática las reglas de los Hooks, los linters necesitan saber qué funciones son Hooks para asegurarse de que no se llamen condicionalmente o dentro de bucles.

Es una buena práctica nombrar los Hooks de manera que tengan sentido semántico. Por ejemplo, si deseas crear un Hook personalizado para un campo de entrada, lo llamarías `useInputField`. Esto garantiza que, al usar el Hook, quede claro de inmediato para qué sirve.

---

### Aplicación de las reglas de los Hooks

Si nos apegamos a la convención de anteponer el prefijo `use` a las funciones de Hook, podemos hacer cumplir automáticamente las otras dos reglas:

1. **Llamar a los Hooks únicamente desde componentes funcionales de React o desde otros Hooks personalizados.**
2. **Llamar a los Hooks únicamente en el nivel superior** (no dentro de bucles, condiciones o funciones anidadas).

Para hacer cumplir las reglas automáticamente, el equipo de React proporciona un plugin oficial de ESLint llamado **`eslint-plugin-react-hooks`**, el cual detecta automáticamente cuándo se utilizan los Hooks y garantiza que no se violen sus reglas. ESLint es un linter que analiza el código fuente y encuentra problemas de estilo, posibles errores y malas prácticas de programación.

Vite ya configura automáticamente ESLint con los plugins pertinentes de React en nuestros proyectos. En el Capítulo 2 (*Uso del Hook de Estado*), tuvimos que deshabilitar específicamente el linter cuando agregamos deliberadamente un Hook condicional para la demostración, añadiendo la siguiente línea:

```javascript
// eslint-disable-next-line react-hooks/rules-of-hooks
```

Si elimináramos esta línea, ESLint mostraría inmediatamente un error indicando que se han violado las reglas de los Hooks.

---

### Resumen

En este capítulo, aprendimos que solo debemos llamar a los Hooks desde componentes funcionales de React (o desde otros Hooks) y que debemos asegurarnos de que el orden de los Hooks se mantenga siempre idéntico en cada renderizado. Además, aprendimos sobre la convención de nomenclatura de los Hooks, los cuales siempre deben comenzar con el prefijo `use`. Por último, vimos cómo ESLint nos ayuda a hacer cumplir estas reglas automáticamente.

Conocer y respetar las reglas de los Hooks es indispensable para evitar fallos y comportamientos inesperados. Estas reglas serán especialmente importantes a la hora de crear nuestros propios Hooks personalizados.

En el próximo capítulo, aprenderemos a crear nuestros propios Hooks personalizados.

---

### Preguntas

Para repasar lo aprendido en este capítulo, intenta responder a las siguientes preguntas:

1. ¿Dónde se pueden invocar los Hooks?
2. ¿Podemos usar Hooks en componentes de clase de React?
3. ¿Qué debemos tener en cuenta con respecto al orden de los Hooks?
4. ¿Se pueden invocar Hooks dentro de condiciones, bucles o funciones anidadas?
5. ¿Cuál es la convención de nomenclatura para los Hooks?
6. ¿Cómo se hacen cumplir las reglas de los Hooks automáticamente?

---

### Lecturas complementarias

Si estás interesado en obtener más información sobre los conceptos que hemos aprendido en este capítulo, consulta el siguiente enlace:

- Reglas de los Hooks en la documentación oficial de React: [https://react.dev/reference/rules/rules-of-hooks](https://react.dev/reference/rules/rules-of-hooks)

#### Más información en Discord

Para unirte a la comunidad de Discord de este libro, donde puedes compartir comentarios, hacer preguntas al autor y enterarte de nuevos lanzamientos, visita el siguiente enlace:
[https://packt.link/wnXT0](https://subscription.packtpub.com/book/web-development/9781836209171/11)
