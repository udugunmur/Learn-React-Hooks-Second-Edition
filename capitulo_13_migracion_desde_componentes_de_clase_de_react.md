# Parte 3: Refactorización y migración de código existente

## Capítulo 13: Migración desde componentes de clase de React

En el capítulo anterior, aprendimos cómo construir nuestros propios Hooks extrayendo Hooks personalizados a partir de código existente. Luego, utilizamos nuestros Hooks personalizados en la aplicación de blog. Finalmente, aprendimos cómo escribir pruebas para Hooks utilizando React Testing Library y escribimos pruebas para nuestros Hooks personalizados.

En este capítulo, comenzaremos implementando una aplicación de lista de tareas pendientes (*todo app*) utilizando componentes de clase de React. Luego, aprenderemos cómo migrar una aplicación existente basada en componentes de clase a React Hooks. Ver las diferencias prácticas entre los Hooks y los componentes de clase profundizará nuestra comprensión sobre las ventajas y desventajas (*trade-offs*) de utilizar cada solución.

En este capítulo, cubriremos los siguientes temas principales:

- Manejo del estado con componentes de clase de React
- Migración desde componentes de clase de React
- Ventajas y desventajas de los componentes de clase de React frente a los Hooks de React

---

### Requisitos técnicos

Debe estar instalada una versión bastante reciente de **Node.js**. El gestor de paquetes de Node (**npm**) también debe estar instalado (debería venir incluido con Node.js). Para obtener más información sobre cómo instalar Node.js, consulta su sitio web oficial: [https://nodejs.org/](https://nodejs.org/).

Utilizaremos **Visual Studio Code (VS Code)** para las guías de este libro, pero todo debería funcionar de manera similar en cualquier otro editor. Para obtener más información sobre cómo instalar VS Code, consulta su sitio web oficial: [https://code.visualstudio.com](https://code.visualstudio.com/).

En este libro, utilizamos las siguientes versiones:

- **Node.js** v22.14.0
- **npm** v10.9.2
- **Visual Studio Code** v1.97.2

Las versiones mencionadas en la lista anterior son las utilizadas en el libro. Aunque instalar una versión más reciente no debería ser un problema, ten en cuenta que ciertos pasos podrían funcionar de manera diferente en una versión más nueva. Si tienes algún problema con el código y los pasos proporcionados en este libro, intenta utilizar las versiones mencionadas.

Puedes encontrar el código de este capítulo en GitHub: [https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter13](https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter13).

Se recomienda encarecidamente que escribas el código por tu cuenta. No te limites a ejecutar simplemente los ejemplos de código proporcionados con el libro. Es importante escribir el código tú mismo para poder aprenderlo y comprenderlo adecuadamente. Sin embargo, si encuentras algún problema, siempre puedes consultar el ejemplo de código.

---

### Manejo del estado con componentes de clase de React

Antes de comenzar a migrar de componentes de clase a Hooks, crearemos una pequeña aplicación de lista de tareas pendientes utilizando componentes de clase. Después, convertiremos estos componentes de clase en componentes funcionales utilizando Hooks. Finalmente, compararemos ambas soluciones.

#### Diseño de la estructura de la aplicación

Al igual que hicimos con la aplicación de blog, comenzaremos definiendo los elementos necesarios:

- Un encabezado (*header*)
- Una forma de agregar nuevas tareas pendientes
- Una lista para mostrar todas las tareas
- Un filtro para las tareas pendientes

Identificamos los siguientes componentes:
- `App`: Componente contenedor principal.
- `Header`: Encabezado.
- `AddTodo`: Formulario para añadir tareas.
- `TodoList`: Renderiza la lista de tareas.
- `TodoItem`: Representa una tarea individual con su casilla de verificación y botón de eliminación.
- `TodoFilter` (+ `TodoFilterItem`): Filtros para alternar entre todas, activas y completadas.

#### Inicialización del proyecto

1. Copia la carpeta `Chapter01_3` a una nueva carpeta `Chapter13_1` ejecutando el siguiente comando:

```bash
cp -R Chapter01_3 Chapter13_1
```

2. Abre la nueva carpeta `Chapter13_1` en VS Code.
3. Elimina el archivo `src/App.jsx` existente para crearlo desde cero.

#### Definición de la estructura de la aplicación

1. Crea un nuevo archivo `src/App.jsx`:

```jsx
import React from 'react'
import { Header } from './Header.jsx'
import { AddTodo } from './AddTodo.jsx'
import { TodoList } from './TodoList.jsx'
import { TodoFilter } from './TodoFilter.jsx'

export class App extends React.Component {
  render() {
    return (
      <div style={{ width: '400px' }}>
        <Header />
        <AddTodo />
        <hr />
        <TodoList />
        <hr />
        <TodoFilter />
      </div>
    )
  }
}
```

#### Definición de los componentes estáticos

##### Componente Header

Crea un nuevo archivo `src/Header.jsx`:

```jsx
import React from 'react'

export class Header extends React.Component {
  render() {
    return <h1>ToDo</h1>
  }
}
```

##### Componente AddTodo

Crea un nuevo archivo `src/AddTodo.jsx`:

```jsx
import React from 'react'

export class AddTodo extends React.Component {
  render() {
    return (
      <form>
        <input
          type='text'
          placeholder='enter new task...'
          style={{ width: '350px' }}
        />
        <input type='submit' style={{ float: 'right' }} value='add' />
      </form>
    )
  }
}
```

##### Componente TodoList

Crea un nuevo archivo `src/TodoList.jsx`:

```jsx
import React from 'react'
import { TodoItem } from './TodoItem.jsx'

export class TodoList extends React.Component {
  render() {
    const items = [
      { id: 1, title: 'Finish React Hooks book', completed: true },
      { id: 2, title: 'Promote the book', completed: false },
    ]

    return items.map((item) => <TodoItem {...item} key={item.id} />)
  }
}
```

##### Componente TodoItem

Crea un nuevo archivo `src/TodoItem.jsx`:

```jsx
import React from 'react'

export class TodoItem extends React.Component {
  render() {
    const { title, completed } = this.props
    return (
      <div style={{ width: '400px', height: '25px' }}>
        <input type='checkbox' checked={completed} />
        {title}
        <button type='button' style={{ float: 'right' }}>
          x
        </button>
      </div>
    )
  }
}
```

##### Componente TodoFilter

Crea un nuevo archivo `src/TodoFilter.jsx`:

```jsx
import React from 'react'

export class TodoFilterItem extends React.Component {
  render() {
    const { name } = this.props
    return <button type='button'>{name}</button>
  }
}

export class TodoFilter extends React.Component {
  render() {
    return (
      <div>
        <TodoFilterItem name='all' />
        <TodoFilterItem name='active' />
        <TodoFilterItem name='completed' />
      </div>
    )
  }
}
```

Ejecuta el servidor de desarrollo:

```bash
npm run dev
```

La aplicación se muestra correctamente, pero es estática. Hagámosla dinámica.

#### Implementación del código dinámico con clases

##### Definición de una API simulada

Crea un nuevo archivo `src/api.js`:

```javascript
const mockItems = [
  { id: 1, title: 'Finish React Hooks book', completed: true },
  { id: 2, title: 'Promote the book', completed: false },
]

export function fetchTodos() {
  return new Promise((resolve) => {
    setTimeout(() => resolve(mockItems), 100)
  })
}
```

##### Definición de StateContext

Crea un nuevo archivo `src/StateContext.js`:

```javascript
import { createContext } from 'react'

export const StateContext = createContext([])
```

##### Hacer que el componente App sea dinámico

Edita `src/App.jsx`:

```jsx
import React from 'react'
import { Header } from './Header.jsx'
import { AddTodo } from './AddTodo.jsx'
import { TodoList } from './TodoList.jsx'
import { TodoFilter } from './TodoFilter.jsx'
import { StateContext } from './StateContext.js'
import { fetchTodos } from './api.js'

export class App extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      todos: [],
      filteredTodos: [],
      filter: 'all',
    }
    this.loadTodos = this.loadTodos.bind(this)
    this.addTodo = this.addTodo.bind(this)
    this.toggleTodo = this.toggleTodo.bind(this)
    this.removeTodo = this.removeTodo.bind(this)
    this.filterTodos = this.filterTodos.bind(this)
  }

  componentDidMount() {
    this.loadTodos()
  }

  async loadTodos() {
    const todos = await fetchTodos()
    this.setState({ todos })
    this.filterTodos()
  }

  addTodo(title) {
    const { todos } = this.state
    const newTodo = { id: Date.now(), title, completed: false }
    this.setState({ todos: [newTodo, ...todos] })
    this.filterTodos()
  }

  toggleTodo(id) {
    const { todos } = this.state
    const updatedTodos = todos.map((item) => {
      if (item.id === id) {
        return { ...item, completed: !item.completed }
      }
      return item
    })
    this.setState({ todos: updatedTodos })
    this.filterTodos()
  }

  removeTodo(id) {
    const { todos } = this.state
    const updatedTodos = todos.filter((item) => item.id !== id)
    this.setState({ todos: updatedTodos })
    this.filterTodos()
  }

  applyFilter(todos, filter) {
    switch (filter) {
      case 'active':
        return todos.filter((item) => item.completed === false)
      case 'completed':
        return todos.filter((item) => item.completed === true)
      case 'all':
      default:
        return todos
    }
  }

  filterTodos(filterArg) {
    this.setState(({ todos, filter }) => {
      const newFilter = filterArg ?? filter
      return {
        filter: newFilter,
        filteredTodos: this.applyFilter(todos, newFilter),
      }
    })
  }

  render() {
    const { filter, filteredTodos } = this.state
    return (
      <StateContext.Provider value={filteredTodos}>
        <div style={{ width: '400px' }}>
          <Header />
          <AddTodo addTodo={this.addTodo} />
          <hr />
          <TodoList
            toggleTodo={this.toggleTodo}
            removeTodo={this.removeTodo}
          />
          <hr />
          <TodoFilter filter={filter} filterTodos={this.filterTodos} />
        </div>
      </StateContext.Provider>
    )
  }
}
```

> Observa la necesidad de realizar el enlace manual de `this` (`.bind(this)`) en el constructor para todos los métodos manejadores.

##### Hacer que AddTodo sea dinámico

Edita `src/AddTodo.jsx`:

```jsx
import React from 'react'

export class AddTodo extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      input: '',
    }
    this.handleInput = this.handleInput.bind(this)
    this.handleSubmit = this.handleSubmit.bind(this)
  }

  handleInput(e) {
    this.setState({ input: e.target.value })
  }

  handleSubmit(e) {
    e.preventDefault()
    const { input } = this.state
    const { addTodo } = this.props
    if (input) {
      addTodo(input)
      this.setState({ input: '' })
    }
  }

  render() {
    const { input } = this.state
    return (
      <form onSubmit={this.handleSubmit}>
        <input
          type='text'
          placeholder='enter new task...'
          style={{ width: '350px' }}
          value={input}
          onChange={this.handleInput}
        />
        <input
          type='submit'
          style={{ float: 'right' }}
          value='add'
          disabled={!input}
        />
      </form>
    )
  }
}
```

##### Hacer que TodoList sea dinámico

Edita `src/TodoList.jsx`:

```jsx
import React from 'react'
import { StateContext } from './StateContext.js'
import { TodoItem } from './TodoItem.jsx'

export class TodoList extends React.Component {
  static contextType = StateContext

  render() {
    const items = this.context
    return items.map((item) => (
      <TodoItem {...item} {...this.props} key={item.id} />
    ))
  }
}
```

> Con componentes de clase, para consumir múltiples contextos se requeriría anidar componentes `<Context.Consumer>`, lo que produce una estructura anidada difícil de mantener.

##### Hacer que TodoItem sea dinámico

Edita `src/TodoItem.jsx`:

```jsx
import React from 'react'

export class TodoItem extends React.Component {
  constructor(props) {
    super(props)
    this.handleToggle = this.handleToggle.bind(this)
    this.handleRemove = this.handleRemove.bind(this)
  }

  handleToggle() {
    const { toggleTodo, id } = this.props
    toggleTodo(id)
  }

  handleRemove() {
    const { removeTodo, id } = this.props
    removeTodo(id)
  }

  render() {
    const { title, completed } = this.props
    return (
      <div style={{ width: '400px', height: '25px' }}>
        <input
          type='checkbox'
          checked={completed}
          onChange={this.handleToggle}
        />
        {title}
        <button
          type='button'
          style={{ float: 'right' }}
          onClick={this.handleRemove}
        >
          x
        </button>
      </div>
    )
  }
}
```

##### Hacer que TodoFilter sea dinámico

Edita `src/TodoFilter.jsx`:

```jsx
import React from 'react'

export class TodoFilterItem extends React.Component {
  constructor(props) {
    super(props)
    this.handleFilter = this.handleFilter.bind(this)
  }

  handleFilter() {
    const { name, filterTodos } = this.props
    filterTodos(name)
  }

  render() {
    const { name, filter = 'all' } = this.props
    return (
      <button
        type='button'
        disabled={filter === name}
        onClick={this.handleFilter}
      >
        {name}
      </button>
    )
  }
}

export class TodoFilter extends React.Component {
  render() {
    return (
      <div>
        <TodoFilterItem {...this.props} name='all' />
        <TodoFilterItem {...this.props} name='active' />
        <TodoFilterItem {...this.props} name='completed' />
      </div>
    )
  }
}
```

Inicia la aplicación:

```bash
npm run dev
```

Ahora todos los componentes funcionan de forma dinámica utilizando componentes de clase.

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter13/Chapter13_1`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Migración desde componentes de clase de React

Ahora migraremos este proyecto a React Hooks, convirtiendo las clases en componentes funcionales.

1. Copia la carpeta `Chapter13_1` a una nueva carpeta `Chapter13_2`:

```bash
cp -R Chapter13_1 Chapter13_2
```

2. Abre `Chapter13_2` en VS Code.

#### Migración del componente TodoItem

Edita `src/TodoItem.jsx`:

```jsx
export function TodoItem({ title, completed, id, toggleTodo, removeTodo }) {
  function handleToggle() {
    toggleTodo(id)
  }

  function handleRemove() {
    removeTodo(id)
  }

  return (
    <div style={{ width: '400px', height: '25px' }}>
      <input type='checkbox' checked={completed} onChange={handleToggle} />
      {title}
      <button type='button' style={{ float: 'right' }} onClick={handleRemove}>
        x
      </button>
    </div>
  )
}
```

> Los componentes funcionales no requieren enlaces de `this` ni constructores, y permiten desestructurar las props directamente en los parámetros.

#### Migración del componente TodoList

Edita `src/TodoList.jsx`:

```jsx
import { useContext } from 'react'
import { StateContext } from './StateContext.js'
import { TodoItem } from './TodoItem.jsx'

export function TodoList(props) {
  const items = useContext(StateContext)
  return items.map((item) => <TodoItem {...item} {...props} key={item.id} />)
}
```

#### Migración del componente TodoFilter

Edita `src/TodoFilter.jsx`:

```jsx
export function TodoFilterItem({ name, filterTodos, filter = 'all' }) {
  function handleFilter() {
    filterTodos(name)
  }

  return (
    <button
      type='button'
      disabled={filter === name}
      onClick={handleFilter}
    >
      {name}
    </button>
  )
}

export function TodoFilter(props) {
  return (
    <div>
      <TodoFilterItem {...props} name='all' />
      <TodoFilterItem {...props} name='active' />
      <TodoFilterItem {...props} name='completed' />
    </div>
  )
}
```

#### Migración del componente AddTodo

Edita `src/AddTodo.jsx`:

```jsx
import { useState } from 'react'

export function AddTodo({ addTodo }) {
  const [input, setInput] = useState('')

  function handleInput(e) {
    setInput(e.target.value)
  }

  function handleSubmit(e) {
    e.preventDefault()
    if (input) {
      addTodo(input)
      setInput('')
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        type='text'
        placeholder='enter new task...'
        style={{ width: '350px' }}
        value={input}
        onChange={handleInput}
      />
      <input
        type='submit'
        style={{ float: 'right' }}
        value='add'
        disabled={!input}
      />
    </form>
  )
}
```

#### Migración de la gestión del estado y del componente App

##### Definición de las acciones

- `LOAD_TODOS`: `{ type: 'LOAD_TODOS', todos: [] }`
- `ADD_TODO`: `{ type: 'ADD_TODO', title: 'Test todo app' }`
- `TOGGLE_TODO`: `{ type: 'TOGGLE_TODO', id: 'xxx' }`
- `REMOVE_TODO`: `{ type: 'REMOVE_TODO', id: 'xxx' }`
- `FILTER_TODOS`: `{ type: 'FILTER_TODOS', filter: 'completed' }`

##### Definición de los reducers

Crea un nuevo archivo `src/reducers.js`:

```javascript
function filterReducer(state, action) {
  if (action.type === 'FILTER_TODOS') {
    return action.filter
  }
  return state
}

function todosReducer(state, action) {
  switch (action.type) {
    case 'LOAD_TODOS':
      return action.todos
    case 'ADD_TODO': {
      const newTodo = { id: Date.now(), title: action.title, completed: false }
      return [newTodo, ...state]
    }
    case 'TOGGLE_TODO': {
      return state.map((item) => {
        if (item.id === action.id) {
          return { ...item, completed: !item.completed }
        }
        return item
      })
    }
    case 'REMOVE_TODO': {
      return state.filter((item) => item.id !== action.id)
    }
    default:
      return state
  }
}

export function appReducer(state, action) {
  return {
    todos: todosReducer(state.todos, action),
    filter: filterReducer(state.filter, action),
  }
}
```

##### Migración del componente App

Edita `src/App.jsx`:

```jsx
import { useReducer, useEffect, useMemo } from 'react'
import { Header } from './Header.jsx'
import { AddTodo } from './AddTodo.jsx'
import { TodoList } from './TodoList.jsx'
import { TodoFilter } from './TodoFilter.jsx'
import { StateContext } from './StateContext.js'
import { fetchTodos } from './api.js'
import { appReducer } from './reducers.js'

export function App() {
  const [state, dispatch] = useReducer(appReducer, {
    todos: [],
    filter: 'all',
  })
  const { todos, filter } = state

  const filteredTodos = useMemo(() => {
    switch (filter) {
      case 'active':
        return todos.filter((item) => item.completed === false)
      case 'completed':
        return todos.filter((item) => item.completed === true)
      case 'all':
      default:
        return todos
    }
  }, [todos, filter])

  useEffect(() => {
    async function loadTodos() {
      const todos = await fetchTodos()
      dispatch({ type: 'LOAD_TODOS', todos })
    }
    void loadTodos()
  }, [])

  function addTodo(title) {
    dispatch({ type: 'ADD_TODO', title })
  }

  function toggleTodo(id) {
    dispatch({ type: 'TOGGLE_TODO', id })
  }

  function removeTodo(id) {
    dispatch({ type: 'REMOVE_TODO', id })
  }

  function filterTodos(filter) {
    dispatch({ type: 'FILTER_TODOS', filter })
  }

  return (
    <StateContext.Provider value={filteredTodos}>
      <div style={{ width: '400px' }}>
        <Header />
        <AddTodo addTodo={addTodo} />
        <hr />
        <TodoList toggleTodo={toggleTodo} removeTodo={removeTodo} />
        <hr />
        <TodoFilter filter={filter} filterTodos={filterTodos} />
      </div>
    </StateContext.Provider>
  )
}
```

Inicia la aplicación:

```bash
npm run dev
```

La aplicación funciona de manera idéntica pero con un código mucho más limpio, desacoplado y mantenible.

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter13/Chapter13_2`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Ventajas y desventajas de los componentes de clase de React frente a los Hooks de React

Con componentes funcionales y Hooks, eliminamos:
- Constructores innecesarios.
- La complejidad y los errores relacionados con el enlace de `this` (`.bind(this)`).
- La desestructuración repetitiva de `this.props` y `this.state`.
- La complejidad en el consumo de múltiples contextos.
- La duplicación de lógica en métodos de ciclo de vida como `componentDidMount` y `componentDidUpdate`.

Además, los componentes funcionales con Hooks:
- Fomentan la creación de componentes pequeños y reutilizables.
- Son más fáciles de refactorizar y probar.
- Requieren menos líneas de código repetitivo (*boilerplate*).
- Son más declarativos.
- Son obligatorios para **React Server Components (RSC)**.

#### ¿Cuándo pueden seguir siendo válidas las clases?
- Al trabajar con bases de código legadas donde la migración completa no es prioritaria.
- Al interactuar con ciertas bibliotecas que aún dependen de componentes de clase (como Error Boundaries tradicionales).

> No es necesario migrar todo un proyecto a Hooks de golpe. React permite combinar libremente componentes funcionales con componentes de clase. La mejor estrategia es adoptar Hooks de forma gradual a medida que se refactorizan o crean nuevos componentes.

---

### Resumen

En este capítulo, construimos primero una aplicación de lista de tareas utilizando componentes de clase de React. Luego, migramos cada componente y la gestión del estado a componentes funcionales con React Hooks (`useReducer`, `useEffect`, `useMemo`, `useContext`, `useState`). Finalmente, analizamos las ventajas y desventajas de ambas soluciones y las estrategias para migraciones graduales en proyectos existentes.

Este capítulo marca el final del libro. A lo largo de esta obra, hemos aprendido:
- Los fundamentos de React y los React Hooks.
- El funcionamiento interno del Hook de Estado (`useState`) y la resolución de limitaciones.
- La gestión avanzada del estado con `useReducer` y efectos con `useEffect`.
- La administración del estado global y temas con `useContext`.
- La obtención de datos con TanStack Query y React Suspense.
- El manejo moderno de formularios con `useActionState`, `useOptimistic` y `useTransition`.
- El enrutamiento en el cliente con React Router y Hooks de navegación.
- Todos los Hooks integrados y avanzados de React 19.
- El ecosistema de Hooks desarrollados por la comunidad.
- Las reglas de los Hooks, la creación de Hooks personalizados y sus pruebas unitarias con Vitest y React Testing Library.
- La migración de componentes de clase a arquitecturas basadas en Hooks.

---

### Preguntas

Para repasar lo aprendido en este capítulo, intenta responder a las siguientes preguntas:

1. ¿Cómo se definen los componentes de clase en React?
2. ¿Qué debemos invocar al usar un constructor en componentes de clase y por qué?
3. ¿Cómo se inicializa y actualiza el estado en componentes de clase?
4. ¿Por qué es necesario enlazar el contexto de `this` en los métodos de clase y cómo se hace?
5. ¿Cómo se consume React Context en componentes de clase?
6. ¿Cómo podemos sustituir la gestión de estado de componentes de clase al migrar a Hooks?
7. ¿Cuáles son las principales ventajas de los Hooks frente a los componentes de clase?
8. ¿Cuándo y cómo se debe migrar un proyecto existente a React Hooks?

---

### Lecturas complementarias

Si estás interesado en obtener más información sobre los conceptos que hemos aprendido en este capítulo, consulta los siguientes enlaces:

- Clases en JavaScript (MDN): [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)
- Componentes de clase en React: [https://www.robinwieruch.de/react-component-types/#react-class-components](https://www.robinwieruch.de/react-component-types/#react-class-components)
- Guía de migración de componentes de clase a funciones en la documentación de React: [https://react.dev/reference/react/Component#migrating-a-simple-component-from-a-class-to-a-function](https://react.dev/reference/react/Component#migrating-a-simple-component-from-a-class-to-a-function)
