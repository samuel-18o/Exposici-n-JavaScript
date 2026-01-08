# Objetos, Referencia y Mutabilidad en JavaScript

Este repositorio contiene material educativo sobre el comportamiento de los objetos en JavaScript, el manejo de referencias, la mutabilidad y su impacto en la gestión del estado de una aplicación.

## 1. Paso por Valor vs Paso por Referencia

En JavaScript, cuando asignamos una variable a otra, no siempre se comporta igual. Esto depende de si el dato se pasa por **valor** o por **referencia**.

### Paso por valor (se copia)
Los datos simples se copian:

```javascript
let a = 5;
let b = a;

b = 10;

console.log(a); // 5
console.log(b); // 10
```

Cambiar `b` no afecta a `a` porque solo se copió el valor.

### Paso por referencia (se comparte)
Los objetos y arrays se comparten:

```javascript
let persona1 = { nombre: "Ana" };
let persona2 = persona1;

persona2.nombre = "Laura";

console.log(persona1.nombre); // "Laura"
```

Ambas variables apuntan al mismo objeto en memoria.

**Ejemplo con arrays:**

```javascript
let nums1 = [1, 2, 3];
let nums2 = nums1;

nums2.push(4);

console.log(nums1); // [1, 2, 3, 4]
```

### Cómo evitar modificar el original
Para crear una copia real del objeto:

```javascript
let usuario = { edad: 18 };
let copia = { ...usuario };

copia.edad = 25;

console.log(usuario.edad); // 18
```

**Resumen rápido:**
*   Primitivos -> se copian (valor)
*   Objetos y arrays -> se comparten (referencia)

---

## 2. Mutación de objetos

La mutación se refiere a la acción de modificar las propiedades internas de un objeto o los elementos de un array existente. Es fundamental entender la diferencia entre **reasignación** y **mutación**, especialmente al usar `const`.

### El mito de const
Declarar un objeto con `const` no lo hace inmutable. `const` solo impide que reasignes la variable (que la apuntes a otro lugar), pero permite cambiar el contenido de la estructura (mutarla).

**Ejemplo de Mutación (Permitido):**
Aquí modificamos el contenido del objeto. La referencia en memoria sigue siendo la misma.

```javascript
const perro = { nombre: "Firulais", edad: 3 };

// Esto es mutación y ES válido con const
perro.edad = 4;
perro.raza = "Pastor Alemán";

console.log(perro); 
// { nombre: "Firulais", edad: 4, raza: "Pastor Alemán" }
```

**Ejemplo de Reasignación (Prohibido):**
Aquí intentamos cambiar la referencia de la variable.

```javascript
const perro = { nombre: "Firulais" };

// Esto lanzará un error porque intentamos reasignar la variable
perro = { nombre: "Rex" }; // Uncaught TypeError: Assignment to constant variable.
```

### ¿Por qué es importante?
Entender la mutación es clave para evitar efectos secundarios ("side effects") en el código. Si pasas un objeto a una función y esa función lo muta, el cambio afectará a cualquier otra parte del programa que use ese objeto.

---

## 3. Spread operator en JavaScript

El spread operator es un operador introducido en ES6 que se representa con tres puntos (`...`). Su función principal es expandir los elementos de una estructura iterable (como arrays, objetos o strings) en un nuevo contexto.

En lugar de trabajar con la estructura completa, el spread operator permite extraer sus valores individuales y utilizarlos donde se necesiten. Esto facilita la copia, combinación y manipulación de datos de forma más clara, concisa e inmutable (sin modificar los valores originales).

### Casos puntuales de uso del spread operator

**1. Copiar arrays u objetos (sin modificar el original)**
Se usa cuando necesitas una copia independiente.

```javascript
const numeros = [1, 2, 3];
const copia = [...numeros];

const usuario = { nombre: "Ana", edad: 25 };
const copiaUsuario = { ...usuario };
```

**2. Unir o combinar estructuras**
Permite juntar varios arrays u objetos en uno solo.

```javascript
// Arrays
const a = [1, 2];
const b = [3, 4];
const resultado = [...a, ...b];

// Objetos
const base = { nombre: "Luis" };
const extra = { edad: 30 };
const combinado = { ...base, ...extra };
```

**3. Agregar o modificar valores**
Se usa para añadir elementos o actualizar propiedades fácilmente.

```javascript
const colores = ["rojo", "azul"];
const nuevosColores = [...colores, "verde"];

const persona = { nombre: "Juan", edad: 20 };
const actualizada = { ...persona, edad: 21 };
```

**4. Pasar argumentos a funciones**
Convierte un array en argumentos individuales.

```javascript
function suma(a, b, c) {
  return a + b + c;
}

const valores = [1, 2, 3];
suma(...valores);
```

**5. Convertir estructuras iterables**
Se puede usar para convertir strings u otros iterables en arrays.

```javascript
const letras = [..."Hola"];
// ["H", "o", "l", "a"]
```

### Consideraciones importantes
*   El spread operator no hace copias profundas, solo superficiales.
*   En objetos, si hay claves repetidas, prevalece la última.
*   No reemplaza métodos complejos, pero simplifica operaciones comunes.

---

## 4. Relación con manejo de estado

El estado es toda la información que un programa guarda y utiliza en un momento específico, como datos de un usuario, configuraciones o valores temporales.

### Objetos y su relación directa con el estado
Un objeto es una estructura que agrupa datos relacionados. Por ejemplo, un objeto puede guardar el nombre, edad y correo de un usuario. En la mayoría de los programas, el estado se guarda dentro de objetos, porque permiten organizar la información de forma clara y reutilizable.

Cada vez que una aplicación cambia de estado, en realidad lo que cambia es el contenido de uno o varios objetos.

### Qué significa trabajar con referencias
Cuando asignamos un objeto a una variable, no se copia el objeto, sino que se guarda una referencia. Una referencia es un acceso directo al mismo objeto en memoria. Esto significa que dos variables pueden apuntar exactamente al mismo estado.

Si una parte del programa modifica ese objeto, el cambio se refleja automáticamente en todas las variables que tengan esa referencia.

### Problemas comunes por mal uso de referencias
En el manejo del estado, esto puede generar errores graves. Por ejemplo, si una función cambia un objeto sin avisar, puede alterar el estado global del programa, provocando resultados inesperados. Por eso, entender las referencias es esencial para saber quién puede modificar el estado y cuándo.

**Ejemplo:**

```javascript
let estadoUsuario = {
  nombre: "Carlos",
  puntos: 10
};

function sumarPuntos(estado) {
  return {
    ...estado,
    puntos: estado.puntos + 5
  };
}

estadoUsuario = sumarPuntos(estadoUsuario);
console.log(estadoUsuario);
```

### Mutabilidad y Buen Manejo del Estado

#### 1. Mutabilidad Directa del Estado (Mala Práctica)
Se modifica directamente el estado global.

```javascript
let estado = { contador: 0 };

function incrementar() {
  estado.contador++;
}
```

*   **Error común:** Permitir que cualquier función cambie el estado sin registrar cuándo ocurrió el cambio.
*   **Problema:** Genera un estado impredecible y bugs difíciles de detectar.

#### 2. Efectos Secundarios
La función cambia algo fuera de su propio alcance.

```javascript
function activar(est) {
  est.activo = true;
}
```

*   **Problema:** El orden de ejecución importa y dos funciones pueden chocar entre sí.

#### 3. Manejo Correcto: Estado Inmutable
No se modifica el objeto original. Se crea un nuevo estado basado en el anterior.

```javascript
function activar(est) {
  return {
    ...est,
    activo: true
  };
}
```

*   **¿Por qué hacerlo así?:** El estado anterior sigue existiendo y los cambios son claros y controlados.
*   **Beneficios:** Flujo de datos predecible, menos errores y código más fácil de mantener.

#### 4. Copia Superficial vs Referencia

*   **Error común:** `let estado1 = { puntos: 10 }; let estado2 = estado1;` (Comparte referencia).
*   **Forma correcta:** `let estado2 = { ...estado1 };` (Crea un nuevo estado).

### Conclusión (Qué aprender de todo esto)
El manejo del estado en JavaScript depende de: saber dónde vive el estado, controlar quién lo modifica y decidir cuándo mutar y cuándo no.

Un buen manejo del estado hace que la aplicación sea:
*   Más estable
*   Más predecible
*   Más fácil de explicar y mantener

**Ejemplo final:**

```javascript
let estadoApp = {
  conectado: false
};

function conectarUsuario(estado) {
  return {
    ...estado,
    conectado: true
  };
}

estadoApp = conectarUsuario(estadoApp);
console.log(estadoApp);
```
