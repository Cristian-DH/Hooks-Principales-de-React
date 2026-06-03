
# Guía básica de los 12 Hooks Más Utilizados de React

## Introducción

Los Hooks fueron incorporados en React 16.8 para permitir que los componentes funcionales tengan estado, ciclo de vida y otras características avanzadas sin necesidad de utilizar clases.

Un Hook es simplemente una función especial proporcionada por React que permite "engancharse" (hook) a funcionalidades internas del framework.

---

# 1. useState()

## ¿Qué es?

useState es el Hook más utilizado de React.

Permite almacenar información dentro de un componente y actualizarla dinámicamente.

Cada vez que el estado cambia, React vuelve a renderizar el componente para mostrar la nueva información.

## Sintaxis

```jsx
const [estado, setEstado] = useState(valorInicial);
```

- estado → valor actual.
- setEstado → función que actualiza el estado.
- valorInicial → valor con el que comienza.

## Ejemplo 

```jsx
import { useState } from "react";

function Contador() {

    // contador almacena el valor actual
    // setContador modifica el valor
    // 0 es el valor inicial
    const [contador, setContador] = useState(0);

    return (
        <div>

            {/* Muestra el valor actual */}
            <h2>Contador: {contador}</h2>

            <button
                onClick={() => {

                    // Actualiza el estado
                    // React vuelve a renderizar
                    setContador(contador + 1);

                }}
            >
                Incrementar
            </button>

        </div>
    );
}
```

## Cuándo usarlo

- Formularios.
- Contadores.
- Mostrar/Ocultar componentes.
- Datos que cambian frecuentemente.

---

# 2. useEffect()

## ¿Qué es?

Permite ejecutar código después de que React renderiza un componente.

Es el Hook que reemplaza gran parte de los métodos de ciclo de vida de las clases.

## Casos de uso

- Consumir APIs.
- Manejar temporizadores.
- Suscribirse a eventos.
- Manipular el DOM.

## Ejemplo 

```jsx
import { useEffect } from "react";

function App() {

    useEffect(() => {

        // Se ejecuta cuando el componente se monta
        console.log("Componente cargado");

        // Función de limpieza
        return () => {

            console.log("Componente destruido");

        };

    }, []);

    return <h1>Hola Mundo</h1>;
}
```

## Dependencias

```jsx
useEffect(() => {

    console.log("Contador actualizado");

}, [contador]);
```

Solo se ejecutará cuando cambie contador.

---

# 3. useContext()

## ¿Qué es?

Permite compartir información entre componentes sin necesidad de pasar props manualmente por muchos niveles.

## Problema que resuelve

Sin Context:

```text
App
 ↓
Header
 ↓
Menu
 ↓
Boton
```

Con Context:

```text
App
 ↓
Context
 ↓
Cualquier componente puede acceder
```

## Ejemplo 

```jsx
import {
    createContext,
    useContext
} from "react";

// Creación del contexto
const TemaContext = createContext();

function Menu() {

    // Obtiene el valor compartido
    const tema = useContext(TemaContext);

    return (
        <h2>
            Tema: {tema}
        </h2>
    );
}
```

---

# 4. useRef()

## ¿Qué es?

Permite almacenar referencias persistentes.

Su principal característica es que NO provoca renderizados cuando cambia.

## Casos de uso

- Referenciar elementos HTML.
- Controlar focos.
- Guardar valores internos.

## Ejemplo 

```jsx
import { useRef } from "react";

function Formulario() {

    // Referencia al input
    const inputRef = useRef(null);

    const enfocarInput = () => {

        // current apunta al elemento HTML real
        inputRef.current.focus();

    };

    return (
        <>

            {/* Asociamos la referencia */}
            <input ref={inputRef} />

            <button onClick={enfocarInput}>
                Enfocar
            </button>

        </>
    );
}
```

---

# 5. useReducer()

## ¿Qué es?

Alternativa a useState para estados complejos.

Se basa en acciones y un reducer que decide cómo actualizar el estado.

## Flujo

```text
dispatch()
     ↓
 reducer()
     ↓
 nuevo estado
     ↓
 render
```

## Ejemplo 

```jsx
import { useReducer } from "react";

function reducer(state, action) {

    switch(action.type) {

        case "SUMAR":

            // Devuelve un nuevo estado
            return {
                contador: state.contador + 1
            };

        default:
            return state;
    }
}

function App() {

    const estadoInicial = {
        contador: 0
    };

    const [state, dispatch] =
        useReducer(
            reducer,
            estadoInicial
        );

    return (

        <button
            onClick={() => {

                // Envía una acción
                dispatch({
                    type: "SUMAR"
                });

            }}
        >
            {state.contador}
        </button>

    );
}
```

---

# 6. useMemo()

## ¿Qué es?

Memoriza el resultado de una operación costosa.

Evita cálculos innecesarios.

## Ejemplo 

```jsx
import { useMemo } from "react";

function App({ numeros }) {

    const suma = useMemo(() => {

        console.log("Calculando suma");

        return numeros.reduce(
            (a, b) => a + b,
            0
        );

    }, [numeros]);

    return <h2>{suma}</h2>;
}
```

## Importante

useMemo memoriza VALORES.

---

# 7. useCallback()

## ¿Qué es?

Memoriza funciones.

Evita que React cree una nueva función en cada render.

## Ejemplo 

```jsx
import { useCallback } from "react";

function App() {

    const saludar = useCallback(() => {

        console.log("Hola");

    }, []);

    return (

        <button
            onClick={saludar}
        >
            Saludar
        </button>

    );
}
```

## Importante

useCallback memoriza FUNCIONES.

---

# 8. useLayoutEffect()

## ¿Qué es?

Funciona parecido a useEffect.

La diferencia es que se ejecuta antes de que el navegador pinte la pantalla.

## Orden

### useEffect

```text
Render
 ↓
Pintado
 ↓
Effect
```

### useLayoutEffect

```text
Render
 ↓
LayoutEffect
 ↓
Pintado
```

## Ejemplo 

```jsx
import { useLayoutEffect } from "react";

function App() {

    useLayoutEffect(() => {

        console.log(
            "Antes del pintado"
        );

    }, []);

    return <h1>Hola</h1>;
}
```

---

# 9. useImperativeHandle()

## ¿Qué es?

Permite exponer funciones de un componente hijo para que el padre pueda ejecutarlas.

## Ejemplo 

```jsx
import {
    forwardRef,
    useImperativeHandle
} from "react";

const Input = forwardRef((props, ref) => {

    useImperativeHandle(ref, () => ({

        limpiar() {

            // Método accesible desde el padre
            console.log("Limpiando");

        }

    }));

    return <input />;
});
```

---

# 10. useId()

## ¿Qué es?

Genera identificadores únicos.

Muy útil para accesibilidad y formularios.

## Ejemplo 

```jsx
import { useId } from "react";

function Formulario() {

    // React genera un ID único
    const id = useId();

    return (

        <>

            <label htmlFor={id}>
                Nombre
            </label>

            <input id={id} />

        </>

    );
}
```

---

# 11. useTransition()

## ¿Qué es?

Permite marcar actualizaciones como no urgentes.

React puede ejecutarlas en segundo plano.

## Ventajas

- Interfaces más fluidas.
- Mejor experiencia de usuario.
- Menos bloqueos visuales.

## Ejemplo 

```jsx
import {
    useState,
    useTransition
} from "react";

function Busqueda() {

    const [texto, setTexto] = useState("");

    // isPending indica si React sigue procesando
    const [isPending, startTransition] =
        useTransition();

    const buscar = (valor) => {

        startTransition(() => {

            // Actualización no urgente
            setTexto(valor);

        });

    };

    return (

        <>

            <input
                onChange={(e) =>
                    buscar(e.target.value)
                }
            />

            {isPending && (
                <p>Cargando...</p>
            )}

        </>

    );
}
```

---

# 12. useDeferredValue()

## ¿Qué es?

Permite retrasar actualizaciones pesadas.

Muy útil cuando existen listas enormes o filtros complejos.

## Ejemplo 

```jsx
import {
    useState,
    useDeferredValue
} from "react";

function Busqueda() {

    const [texto, setTexto] = useState("");

    // React puede retrasar esta actualización
    const textoDiferido =
        useDeferredValue(texto);

    return (

        <>

            <input
                value={texto}
                onChange={(e) =>
                    setTexto(e.target.value)
                }
            />

            {/* Usa la versión diferida */}
            <p>{textoDiferido}</p>

        </>

    );
}
```

---

# Tabla Resumen de Hooks de React

| Hook | Memoriza | Re-renderiza | Uso Principal | Función |
|--------|--------|--------|--------|--------|
| `useState` | No | Sí | Estado | Guarda y actualiza valores que cambian durante la vida del componente. |
| `useEffect` | No | No | Efectos | Ejecuta código secundario después del renderizado, como llamadas a APIs, temporizadores o eventos. |
| `useContext` | No | Sí | Datos globales | Permite acceder a información compartida entre componentes sin necesidad de pasar props manualmente. |
| `useRef` | No | No | Referencias | Almacena referencias al DOM o valores persistentes sin provocar nuevos renderizados. |
| `useReducer` | No | Sí | Estado complejo | Administra estados complejos mediante acciones y una función reductora (reducer). |
| `useMemo` | Valores | No | Optimización | Memoriza el resultado de cálculos costosos para evitar ejecutarlos innecesariamente. |
| `useCallback` | Funciones | No | Optimización | Memoriza funciones para evitar recrearlas en cada renderizado. |
| `useLayoutEffect` | No | No | Antes del pintado | Ejecuta efectos de forma síncrona antes de que el navegador actualice la pantalla. |
| `useImperativeHandle` | No | No | Exponer métodos | Permite que un componente hijo exponga métodos personalizados a su componente padre. |
| `useId` | No | No | IDs únicos | Genera identificadores únicos y estables para mejorar accesibilidad y formularios. |
| `useTransition` | No | Sí | Actualizaciones lentas | Marca actualizaciones como no urgentes para mantener la interfaz fluida. |
| `useDeferredValue` | Valores | No | Diferir renderizados | Retrasa la actualización de un valor para evitar bloqueos cuando hay mucho trabajo de renderizado. |

