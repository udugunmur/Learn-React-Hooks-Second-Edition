# Parte 3: Refactorización y migración de código existente

## Capítulo 12: Creación de tus propios Hooks

En el capítulo anterior, aprendimos sobre las limitaciones y reglas de los Hooks. También aprendimos dónde llamar a los Hooks, por qué importa el orden y las convenciones de nomenclatura para los Hooks.

En este capítulo, aprenderemos cómo crear Hooks personalizados extrayendo código existente de nuestros componentes. También aprenderemos cómo usar Hooks personalizados y cómo pueden interactuar entre sí. Finalmente, aprenderemos a escribir pruebas unitarias para nuestros Hooks personalizados.

Al final de este capítulo, podrás crear Hooks personalizados para encapsular y reutilizar la lógica de la aplicación, manteniendo tu código limpio y fácil de mantener.

En este capítulo, cubriremos los siguientes temas principales:

- Creación de un Hook Theme personalizado
- Creación de un Hook User personalizado
- Creación de Hooks de API personalizados
- Creación de un Hook Debounced History State
- Pruebas de Hooks personalizados

---

### Requisitos técnicos

Debe estar instalada una versión bastante reciente de **Node.js**. El gestor de paquetes de Node (**npm**) también debe estar instalado (debería venir incluido con Node.js). Para obtener más información sobre cómo instalar Node.js, consulta su sitio web oficial: [https://nodejs.org/](https://nodejs.org/).

Utilizaremos **Visual Studio Code (VS Code)** para las guías de este libro, pero todo debería funcionar de manera similar en cualquier otro editor. Para obtener más información sobre cómo instalar VS Code, consulta su sitio web oficial: [https://code.visualstudio.com](https://code.visualstudio.com/).

En este libro, utilizamos las siguientes versiones:

- **Node.js** v22.14.0
- **npm** v10.9.2
- **Visual Studio Code** v1.97.2

Las versiones mencionadas en la lista anterior son las utilizadas en el libro. Aunque instalar una versión más reciente no debería ser un problema, ten en cuenta que ciertos pasos podrían funcionar de manera diferente en una versión más nueva. Si tienes algún problema con el código y los pasos proporcionados en este libro, intenta utilizar las versiones mencionadas.

Puedes encontrar el código de este capítulo en GitHub: [https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter12](https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter12).

Se recomienda encarecidamente que escribas el código por tu cuenta. No te limites a ejecutar simplemente los ejemplos de código proporcionados con el libro. Es importante escribir el código tú mismo para poder aprenderlo y comprenderlo adecuadamente. Sin embargo, si encuentras algún problema, siempre puedes consultar el ejemplo de código.

---

### Creación de un Hook Theme personalizado

En el Capítulo 5 (*Implementación de contextos de React*), introdujimos un `ThemeContext` para dar estilo a las publicaciones del blog en nuestra aplicación. Usamos un Hook de Contexto para acceder a `ThemeContext` en muchos componentes:

```javascript
import { ThemeContext } from '@/contexts/ThemeContext.js'

export default function SomeComponent () {
  const theme = useContext(ThemeContext)
  // …
```

Podríamos abstraer esta funcionalidad en un Hook `useTheme`, que obtendrá el objeto de tema directamente desde `ThemeContext`.

> Por lo general, tiene más sentido escribir primero el componente y luego extraer un Hook personalizado si observamos que utilizamos código similar en varios componentes. Hacer esto evita crear Hooks personalizados prematuramente y hacer que nuestro proyecto sea innecesariamente complejo.

#### Creación del Hook Theme personalizado

Comencemos a crear un Hook Theme personalizado extrayendo el código existente del Hook de Contexto en una función separada:

1. Copia la carpeta `Chapter10_3` a una nueva carpeta `Chapter12_1` ejecutando el siguiente comando:

```bash
cp -R Chapter10_3 Chapter12_1
```

2. Abre la nueva carpeta `Chapter12_1` en VS Code.
3. Crea una nueva carpeta `src/hooks/`.
4. Dentro de ella, crea un nuevo archivo `src/hooks/theme.js`.
5. En este archivo recién creado, importa la función `useContext` y `ThemeContext`:

```javascript
import { useContext } from 'react'
import { ThemeContext } from '@/contexts/ThemeContext.js'
```

6. Ahora, define y exporta una función `useTheme`, que simplemente devuelva el Hook de Contexto:

```javascript
export function useTheme() {
  return useContext(ThemeContext)
}
```

¡Es tan simple como eso! Siempre que nos apeguemos a las reglas y convenciones de nomenclatura de los Hooks, podemos crear fácilmente nuestros propios Hooks personalizados.

#### Uso del Hook Theme personalizado

Para comenzar a usar nuestro Hook Theme personalizado:

1. Edita `src/components/post/Post.jsx` y reemplaza las importaciones de `useContext` y `ThemeContext` por:

```javascript
import { useTheme } from '@/hooks/theme.js'
```

2. Reemplaza el Hook de Contexto existente por nuestro Hook Theme personalizado:

```javascript
export function Post({ id }) {
  const theme = useTheme()
```

3. Edita `src/components/post/PostListItem.jsx` y realiza el mismo cambio:

```javascript
import { useTheme } from '@/hooks/theme.js'

export function PostListItem({ id, title, author }) {
  const theme = useTheme()
```

4. Ejecuta el servidor de desarrollo:

```bash
npm run dev
```

Reemplazar los Hooks de Contexto con nuestro Hook Theme simplifica el código (requiriendo menos importaciones) y nos permite ajustar fácilmente el sistema de temas más adelante en un solo lugar.

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter12/Chapter12_1`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Creación de un Hook User personalizado

Para evitar tener que modificar múltiples componentes si la lógica de autenticación o almacenamiento cambia en el futuro, podemos encapsular toda la información y funciones relacionadas con el usuario en un Hook personalizado `useUser`.

#### Creación del Hook User personalizado

Extraigamos todo nuestro código existente relacionado con el manejo del usuario a un Hook `useUser`:

1. Copia la carpeta `Chapter12_1` a una nueva carpeta `Chapter12_2` ejecutando el siguiente comando:

```bash
cp -R Chapter12_1 Chapter12_2
```

2. Abre la nueva carpeta `Chapter12_2` en VS Code.
3. Crea un nuevo archivo `src/hooks/user.js`.
4. Dentro de él, importa la función `useLocalStorage`:

```javascript
import { useLocalStorage } from '@uidotdev/usehooks'
```

5. Define la función `useUser`, en la cual usamos el Hook Local Storage, definimos la bandera `isLoggedIn` y las funciones auxiliares:

```javascript
export function useUser() {
  const [username, setUsername] = useLocalStorage('username', null)
  const isLoggedIn = username !== null

  function register(username) {
    setUsername(username)
  }

  function login(username) {
    setUsername(username)
  }

  function logout() {
    setUsername(null)
  }

  return { username, isLoggedIn, register, login, logout }
}
```

> Como puedes ver, no solo devolvemos el nombre de usuario y una función para establecerlo, sino un objeto estructurado con información de la sesión y funciones semánticas (`login`, `register`, `logout`).

#### Uso del Hook User personalizado

Refactoricemos la aplicación para usar `useUser`:

1. Edita `src/App.jsx` y reemplaza `useLocalStorage` por `useUser`:

```javascript
import { useUser } from './hooks/user.js'

export function App() {
  const { isLoggedIn } = useUser()
```

```jsx
        {isLoggedIn && <CreatePost />}
```

2. Edita `src/components/user/UserBar.jsx`:

```javascript
import { useUser } from '@/hooks/user.js'

export function UserBar() {
  const { isLoggedIn } = useUser()

  if (isLoggedIn) {
```

3. Edita `src/components/user/Register.jsx`:

```javascript
import { useUser } from '@/hooks/user.js'

export function Register() {
  const { register } = useUser()

  function handleSubmit(e) {
    e.preventDefault()
    const username = e.target.elements.username.value
    register(username)
  }
```

4. Edita `src/components/user/Login.jsx`:

```javascript
import { useUser } from '@/hooks/user.js'

export function Login() {
  const { login } = useUser()

  function handleSubmit(e) {
    e.preventDefault()
    const username = e.target.elements.username.value
    login(username)
  }
```

5. Edita `src/components/user/Logout.jsx`:

```javascript
import { useUser } from '@/hooks/user.js'

export function Logout() {
  const { username, logout } = useUser()

  function handleSubmit(e) {
    e.preventDefault()
    logout()
  }
```

6. Edita `src/components/post/CreatePost.jsx`:

```javascript
import { useUser } from '@/hooks/user.js'

export function CreatePost() {
  const { username } = useUser()
```

7. Edita `src/components/comment/CreateComment.jsx`:

```javascript
import { useUser } from '@/hooks/user.js'

export function CreateComment({ addComment }) {
  const { username } = useUser()
```

8. Edita `src/components/comment/CommentList.jsx`:

```javascript
import { useUser } from '@/hooks/user.js'

export function CommentList() {
  const { isLoggedIn } = useUser()
```

```jsx
      {isLoggedIn && <CreateComment addComment={addComment} />}
```

9. Inicia el servidor de desarrollo:

```bash
npm run dev
```

El código refactorizado con el Hook `useUser` es significativamente más legible y desacopla los componentes de la lógica interna de almacenamiento.

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter12/Chapter12_2`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Creación de Hooks de API personalizados

También podemos crear Hooks para las diversas llamadas a la API. Poner estos Hooks en un solo archivo nos permite ajustar o sustituir la biblioteca de peticiones fácilmente en el futuro. Antepondremos el prefijo `useAPI` a nuestros Hooks personalizados de API.

#### Extracción de Hooks de API personalizados

1. Copia la carpeta `Chapter12_2` a una nueva carpeta `Chapter12_3` ejecutando el siguiente comando:

```bash
cp -R Chapter12_2 Chapter12_3
```

2. Abre la nueva carpeta `Chapter12_3` en VS Code.
3. Crea un nuevo archivo `src/hooks/api.js`.
4. Edita `src/hooks/api.js` y define los Hooks de API:

```javascript
import { useSuspenseQuery, useMutation } from '@tanstack/react-query'
import {
  fetchPosts,
  fetchPost,
  searchPosts,
  createPost,
  queryClient,
} from '@/api.js'

export function useAPIFetchPosts({ featured }) {
  const { data } = useSuspenseQuery({
    queryKey: ['posts', featured],
    queryFn: async () => await fetchPosts({ featured }),
  })
  return data
}

export function useAPIFetchPost({ id }) {
  const { data } = useSuspenseQuery({
    queryKey: ['post', id],
    queryFn: async () => await fetchPost({ id }),
  })
  return data
}

export function useAPISearchPosts({ query }) {
  const { data } = useSuspenseQuery({
    queryKey: ['posts', query],
    queryFn: async () => await searchPosts(query),
  })
  return data
}

export function useAPICreatePost() {
  const createPostMutation = useMutation({
    mutationFn: createPost,
    onSuccess: () => {
      queryClient.invalidateQueries(['posts'])
    },
  })
  return createPostMutation.mutateAsync
}
```

#### Uso de Hooks de API personalizados

Refactoricemos los componentes para usar los Hooks de API:

1. Edita `src/components/post/PostFeed.jsx`:

```jsx
import { useAPIFetchPosts } from '@/hooks/api.js'

export function PostFeed({ featured = false }) {
  const posts = useAPIFetchPosts({ featured })
  return <PostList posts={posts} />
}
```

2. Edita `src/components/post/Post.jsx`:

```jsx
import { useAPIFetchPost } from '@/hooks/api.js'

export function Post({ id }) {
  const theme = useTheme()
  const { title, content, author } = useAPIFetchPost({ id })
  return (
```

3. Edita `src/components/post/PostSearchResults.jsx`:

```jsx
import { useAPISearchPosts } from '@/hooks/api.js'

export function PostSearchResults({ query }) {
  const posts = useAPISearchPosts({ query })
  return <PostList posts={posts} />
}
```

4. Edita `src/components/post/CreatePost.jsx`:

```javascript
import { useAPICreatePost } from '@/hooks/api.js'
```

```javascript
export function CreatePost() {
  const { username } = useUser()
  const navigate = useNavigate()
  const createPost = useAPICreatePost()
```

```javascript
  const [error, submitAction, isPending] = useActionState(
    async (currentState, formData) => {
      const title = formData.get('title')
      const content = formData.get('content')
      const post = { title, content, author: username, featured: false }
      try {
        const result = await createPost(post)
        clear()
        navigate(`/post/${result.id}`)
      } catch (err) {
        return err
      }
    },
  )
```

5. Inicia el servidor de desarrollo:

```bash
npm run dev
```

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter12/Chapter12_3`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Creación de un Hook Debounced History State

Ahora vamos a crear un Hook compuesto más avanzado: `useDebouncedHistoryState`, combinando `useHistoryState` y `useDebouncedCallback`.

#### Creación del Hook

1. Copia la carpeta `Chapter12_3` a una nueva carpeta `Chapter12_4` ejecutando el siguiente comando:

```bash
cp -R Chapter12_3 Chapter12_4
```

2. Abre la nueva carpeta `Chapter12_4` en VS Code.
3. Crea un nuevo archivo `src/hooks/debouncedHistoryState.js`:

```javascript
import { useState, useEffect } from 'react'
import { useDebouncedCallback } from 'use-debounce'
import { useHistoryState } from '@uidotdev/usehooks'

export function useDebouncedHistoryState(initialState, timeout) {
  const { state, set, undo, redo, clear, canUndo, canRedo } =
    useHistoryState(initialState)
  const [content, setContent] = useState(initialState)
  const debounced = useDebouncedCallback((value) => set(value), timeout)

  useEffect(() => {
    debounced.cancel()
    setContent(state)
  }, [state, debounced])

  function handleContentChange(e) {
    const { value } = e.target
    setContent(value)
    debounced(value)
  }

  return {
    content,
    handleContentChange,
    undo,
    redo,
    clear,
    canUndo,
    canRedo,
  }
}
```

#### Uso del Hook Debounced History State

1. Edita `src/components/post/CreatePost.jsx` y reemplaza la lógica anterior por el nuevo Hook:

```javascript
import { useDebouncedHistoryState } from '@/hooks/debouncedHistoryState.js'
```

```javascript
export function CreatePost() {
  const { username } = useUser()
  const navigate = useNavigate()
  const createPost = useAPICreatePost()
  const { content, handleContentChange, undo, redo, clear, canUndo, canRedo } =
    useDebouncedHistoryState('', 200)
```

2. Inicia el servidor de desarrollo y comprueba que la creación de publicaciones y el botón de deshacer/rehacer sigan funcionando perfectamente:

```bash
npm run dev
```

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter12/Chapter12_4`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Pruebas de Hooks personalizados

Al crear Hooks personalizados, es una buena práctica escribir pruebas unitarias para garantizar que funcionen correctamente incluso cuando los modifiquemos o ampliemos más adelante. Utilizaremos **Vitest** y **React Testing Library**.

Dado que los Hooks no se pueden llamar fuera de componentes funcionales de React, `renderHook` de React Testing Library genera internamente un componente ficticio para ejecutar el Hook y nos proporciona funciones para interactuar con él.

#### Configuración de Vitest y React Testing Library

1. Copia la carpeta `Chapter12_4` a una nueva carpeta `Chapter12_5` ejecutando el siguiente comando:

```bash
cp -R Chapter12_4 Chapter12_5
```

2. Abre la nueva carpeta `Chapter12_5` en VS Code.
3. Instala Vitest, React Testing Library y `jsdom`:

```bash
npm install --save-exact --save-dev vitest@3.0.5 @testing-library/react@16.2.0 jsdom@26.0.0
```

4. Edita `package.json` y agrega el script de prueba:

```json
  "scripts": {
    "test": "vitest",
```

5. Edita `vite.config.js` y agrega la configuración para Vitest al final:

```javascript
      rewrite: (path) => path.replace(/^\/api/, ''),
    },
  },
  test: {
    environment: 'jsdom',
  },
})
```

#### Pruebas de un Hook simple (useCounter)

1. Crea un nuevo archivo `src/hooks/counter.js`:

```javascript
import { useState } from 'react'

export function useCounter(initialCount = 0) {
  const [count, setCount] = useState(initialCount)

  function increment() {
    setCount((count) => count + 1)
  }

  function reset() {
    setCount(initialCount)
  }

  return { count, increment, reset }
}
```

2. Crea el archivo de prueba `src/hooks/counter.test.js`:

```javascript
import { describe, test, expect } from 'vitest'
import { renderHook, act } from '@testing-library/react'
import { useCounter } from './counter.js'

describe('Counter Hook', {}, () => {
  test('should return 0 by default', {}, () => {
    const { result } = renderHook(() => useCounter())
    expect(result.current.count).toBe(0)
  })

  test('should initially return initial count', {}, () => {
    const { result } = renderHook(() => useCounter(123))
    expect(result.current.count).toBe(123)
  })

  test('should increment counter when increment() is called', {}, () => {
    const { result } = renderHook(() => useCounter(0))
    act(() => result.current.increment())
    expect(result.current.count).toBe(1)
  })

  test('should reset to initial value', {}, () => {
    let initial = 0
    const { result, rerender } = renderHook(() => useCounter(initial))
    initial = 123
    rerender()
    act(() => result.current.reset())
    expect(result.current.count).toBe(123)
  })
})
```

3. Ejecuta las pruebas:

```bash
npm test
```

#### Pruebas del Hook Theme (con Contexto)

Para probar Hooks que utilizan contexto, proporcionamos un componente envoltorio (*wrapper*) a `renderHook`:

1. Crea un nuevo archivo `src/hooks/theme.test.jsx`:

```jsx
import { describe, test, expect } from 'vitest'
import { renderHook } from '@testing-library/react'
import { ThemeContext } from '@/contexts/ThemeContext.js'
import { useTheme } from './theme.js'

function ThemeContextWrapper({ children }) {
  return (
    <ThemeContext.Provider value={{ primaryColor: 'deepskyblue' }}>
      {children}
    </ThemeContext.Provider>
  )
}

describe('Theme Hook', {}, () => {
  test('should return the primaryColor defined by the context', {}, () => {
    const { result } = renderHook(() => useTheme(), {
      wrapper: ThemeContextWrapper,
    })
    expect(result.current.primaryColor).toBe('deepskyblue')
  })
})
```

#### Pruebas del Hook User

1. Crea el archivo `src/hooks/user.test.js`:

```javascript
import { describe, test, expect } from 'vitest'
import { renderHook, act } from '@testing-library/react'
import { useUser } from './user.js'

describe('User Hook', {}, () => {
  test('should not be logged in by default', {}, () => {
    const { result } = renderHook(() => useUser())
    expect(result.current.isLoggedIn).toBe(false)
    expect(result.current.username).toBe(null)
  })

  test('should be logged in after registering', {}, () => {
    const { result } = renderHook(() => useUser())
    act(() => result.current.register('testuser'))
    expect(result.current.isLoggedIn).toBe(true)
    expect(result.current.username).toBe('testuser')
  })

  test('should be logged in after logging in', {}, () => {
    const { result } = renderHook(() => useUser())
    act(() => result.current.login('testuser'))
    expect(result.current.isLoggedIn).toBe(true)
    expect(result.current.username).toBe('testuser')
  })

  test('should be logged out after logout', {}, () => {
    const { result } = renderHook(() => useUser())
    act(() => result.current.login('testuser'))
    act(() => result.current.logout())
    expect(result.current.isLoggedIn).toBe(false)
    expect(result.current.username).toBe(null)
  })
})
```

#### Pruebas de Hooks asíncronos (waitFor)

Para Hooks con operaciones asíncronas o temporizadores, utilizamos `waitFor`:

1. Crea el archivo `src/hooks/debouncedHistoryState.test.js`:

```javascript
import { describe, test, expect } from 'vitest'
import { renderHook, act, waitFor } from '@testing-library/react'
import { useDebouncedHistoryState } from './debouncedHistoryState.js'

describe('Debounced History State Hook', {}, () => {
  test('should return initial state as content', {}, () => {
    const { result } = renderHook(() => useDebouncedHistoryState('', 10))
    expect(result.current.content).toBe('')
  })

  test('should update content immediately', {}, () => {
    const { result } = renderHook(() => useDebouncedHistoryState('', 10))
    act(() =>
      result.current.handleContentChange({
        target: { value: 'new content' },
      }),
    )
    expect(result.current.content).toBe('new content')
  })

  test('should only update history state after debounce', {}, async () => {
    const { result } = renderHook(() => useDebouncedHistoryState('', 10))
    act(() =>
      result.current.handleContentChange({
        target: { value: 'new content' },
      }),
    )
    expect(result.current.canUndo).toBe(false)
    await waitFor(() => {
      expect(result.current.canUndo).toBe(true)
    })
  })
})
```

2. Ejecuta todos los tests para verificar que todos pasen:

```bash
npm test
```

#### Código de ejemplo

El código de ejemplo para esta sección se puede encontrar en la carpeta `Chapter12/Chapter12_5`. Consulta el archivo `README.md` dentro de la carpeta para obtener instrucciones sobre cómo configurar y ejecutar el ejemplo.

---

### Resumen

En este capítulo, aprendimos cómo extraer Hooks personalizados a partir del código existente de nuestra aplicación de blog. Definimos el Hook `useTheme` para acceder fácilmente al contexto, el Hook `useUser` para gestionar el estado del usuario, los Hooks `useAPI` para centralizar peticiones de red y el Hook compuesto `useDebouncedHistoryState`. Finalmente, aprendimos a escribir pruebas unitarias para nuestros Hooks personalizados utilizando Vitest y React Testing Library, abarcando Hooks síncronos, con contexto y asíncronos.

En el próximo capítulo, aprenderemos cómo migrar aplicaciones existentes basadas en componentes de clase a una arquitectura moderna basada en React Hooks.

---

### Preguntas

Para repasar lo aprendido en este capítulo, intenta responder a las siguientes preguntas:

1. ¿Cómo podemos extraer un Hook personalizado a partir de código existente?
2. ¿Cuál es la ventaja de crear Hooks personalizados?
3. ¿Cuándo deberíamos extraer funcionalidad en un Hook personalizado?
4. ¿Cómo se utilizan los Hooks personalizados en los componentes?
5. ¿Qué bibliotecas podemos utilizar para probar Hooks personalizados?
6. ¿Cómo se prueban las acciones desencadenadas por Hooks mediante `act()`?
7. ¿Cómo podemos probar Hooks que hacen uso de React Context?
8. ¿Cómo podemos probar Hooks que realizan operaciones asíncronas?

---

### Lecturas complementarias

Si estás interesado en obtener más información sobre los conceptos que hemos aprendido en este capítulo, consulta los siguientes enlaces:

- Guía "Reutilizar lógica con Hooks personalizados" en la documentación oficial de React: [https://react.dev/learn/reusing-logic-with-custom-hooks](https://react.dev/learn/reusing-logic-with-custom-hooks)
- Documentación oficial de Vitest: [https://vitest.dev/](https://vitest.dev/)
- Documentación oficial de React Testing Library: [https://testing-library.com/docs/react-testing-library/intro/](https://testing-library.com/docs/react-testing-library/intro/)
