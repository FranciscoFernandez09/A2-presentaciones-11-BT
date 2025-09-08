---
theme: seriph
background: https://cdn.jsdelivr.net/gh/slidevjs/slidev-covers@main/static/d34DtRp1bqo.webp
title: Backtracking
info: |
  ## Presentación sobre Backtracking
  Explicación detallada de la técnica, ejemplos, casos de uso y template general.
class: text-center
highlighter: shiki
drawings:
  persist: false
transition: slide-left
lineNumbers: true
mdc: true
---

# Backtracking

Técnica de diseño de algoritmos para explorar soluciones mediante búsqueda sistemática y retroceso.

---

# ¿Qué es Backtracking?

Backtracking es una técnica que explora todas las posibles soluciones de un problema, retrocediendo (backtrack) cuando una opción no lleva a una solución válida.

Se utiliza para problemas donde se busca:

- Encontrar una solución (de decisión)
- Encontrar todas las soluciones (enumeración)
- Encontrar la mejor solución (de optimización)
- Encontrar las mejores (enumeración + optimización)

---

# Etapas del Backtracking

1. **Elección**: Seleccionar una opción/movimiento posible.

2. **Validación**: Verificar si la opción es válida.

3. **Acción**: Realizar la acción.

4. **Recursión**: Llamar recursivamente para explorar el siguiente paso.

5. **Deshacer**: Volver atrás (backtrack) para probar otras opciones.

---

# Template General de Backtracking

```cpp
void backtracking(Estado estado) {
    // La poda es opcional, solo si el problema lo permite
    if (hayQuePodar(estado)) { return };
    
    if (esSolucion(estado)) { procesarSolucion(estado)};
    
    else {
        for (Movimiento mov : posiblesMovimientos(estado)) {
            if (movimientoValido(estado, mov)) {
                hacerMovimiento(estado, mov);
                backtracking(estado);
                deshacerMovimiento(estado, mov);
            }
        }
    }
}
```
---

# Explicación del template

- **hayQuePodar**: Evita explorar ramas que no pueden llevar a una solución válida. No todos los problemas permiten poda.

- **esSolucion**: Verifica si el estado actual es una solución.

- **posiblesMovimientos**: Genera las opciones a explorar.

- **movimientoValido**: Verifica si un movimiento específico es válido.

- **hacerMovimiento**: Modifica el estado para aplicar el movimiento elegido.

- **backtracking**: Llamada recursiva para explorar el siguiente nivel del árbol de búsqueda.

- **deshacerMovimiento**: Revierte el estado al punto anterior (retroceso).

---

# ¿Dónde se usa Backtracking?

Problemas típicos:

- N-Reinas
- Sudoku  
- Generación de subconjuntos/permutaciones
- Laberintos y caminos

---

# Ejemplo: N-Reinas

Colocar N reinas en un tablero de NxN sin que se ataquen entre sí.

```cpp
bool esSolucion(int fila, int N) {
    return fila == N;
}

bool movimientoValido(int fila, int col, Tablero& t) {
    return puedoColocar(fila, col, t);
}

void hacerMovimiento(int fila, int col, Tablero& t) {
    colocarReina(fila, col, t);
}

void deshacerMovimiento(int fila, int col, Tablero& t) {
    quitarReina(fila, col, t);
}

void nReinasBT(int fila, int N, Tablero& t) {
    if (esSolucion(fila, N)) {
        imprimirTablero(t);
        return;
    }
    for (int col = 0; col < N; col++) {
        if (movimientoValido(fila, col, t)) {
            hacerMovimiento(fila, col, t);
            nReinasBT(fila + 1, N, t);
            deshacerMovimiento(fila, col, t);
        }
    }
}
```

---

# Ejemplo: Sudoku

Resolver un tablero de Sudoku llenando los espacios vacíos.

```cpp
bool esSolucion(Tablero& t) {
    return tableroCompleto(t);
}

bool movimientoValido(int fila, int col, int num, Tablero& t) {
    return puedoColocar(fila, col, num, t);
}

void hacerMovimiento(int fila, int col, int num, Tablero& t) {
    t[fila][col] = num;
}

void deshacerMovimiento(int fila, int col, Tablero& t) {
    t[fila][col] = 0;
}

void sudokuBT(Tablero& t) {
    if (esSolucion(t)) {
        imprimirTablero(t);
        return;
    }
    for (int fila = 0; fila < 9; fila++) {
        for (int col = 0; col < 9; col++) {
            if (t[fila][col] == 0) {
                for (int num = 1; num <= 9; num++) {
                    if (movimientoValido(fila, col, num, t)) {
                        hacerMovimiento(fila, col, num, t);
                        sudokuBT(t);
                        deshacerMovimiento(fila, col, t);
                    }
                }
                return;
            }
        }
    }
}
```

---

# Ejemplo: Generación de subconjuntos

Generar todos los subconjuntos de un conjunto dado.

```cpp
bool esSolucion(int idx, int n) {
    return idx == n;
}

void hacerMovimiento(vector<int>& actual, int elem) {
    actual.push_back(elem);
}

void deshacerMovimiento(vector<int>& actual) {
    actual.pop_back();
}

void subconjuntosBT(vector<int>& conjunto, int idx, vector<int>& actual) {
    if (esSolucion(idx, conjunto.size())) {
        imprimir(actual);
        return;
    }
    // No incluir el elemento
    subconjuntosBT(conjunto, idx + 1, actual);
    // Incluir el elemento
    hacerMovimiento(actual, conjunto[idx]);
    subconjuntosBT(conjunto, idx + 1, actual);
    deshacerMovimiento(actual);
}
```

---

# Ejemplo: Problema de la mochila (Backtracking)

Buscar el máximo valor posible sin exceder la capacidad.

```cpp
bool hayQuePodar(int capacidadRestante) {
    return capacidadRestante < 0;
}

bool esSolucion(int idx, int n) {
    return idx == n;
}

void mochilaBT(int idx, int capacidadRestante, int valorActual, int& mejorValor) {
    if (hayQuePodar(capacidadRestante)) return;
    if (esSolucion(idx, n)) {
        mejorValor = max(mejorValor, valorActual);
        return;
    }
    // No tomar el elemento
    mochilaBT(idx + 1, capacidadRestante, valorActual, mejorValor);
    // Tomar el elemento (si es válido)
    if (pesos[idx] <= capacidadRestante) {
        mochilaBT(idx + 1, capacidadRestante - pesos[idx], valorActual + valores[idx], mejorValor);
    }
}
```

---

# Poda en Backtracking

La **poda** es fundamental para mejorar la eficiencia:

- Evita explorar ramas que no pueden llevar a una solución válida.

- **Diferencia clave**: 
  - **Validación de movimiento**: Verifica si una acción es permitida por las reglas.
  - **Poda real**: Descarta ramas que no pueden mejorar la solución actual.

- **Ejemplo laberinto**: Si queremos llegar de A a B en máximo N movimientos, podamos cuando los movimientos restantes sean menores que la distancia mínima entre la posición actual y B.

---

# Eficiencia y Complejidad en Backtracking

- En el peor caso, **complejidad exponencial**  
  (ej: N-Reinas → O(N!); Subconjuntos → O(2^N)).

- **Sin poda**: se exploran todas las configuraciones posibles.

- **Con poda**: se reduce el árbol de búsqueda, pero sigue siendo exponencial.

- El costo depende de:
  - Tamaño del espacio de búsqueda
  - Calidad de las podas
  - Orden en que se prueban las opciones

<br>

👉 Backtracking no "vence" la complejidad, pero permite resolver problemas de tamaño moderado de forma práctica.

---

# Poda Avanzada y Heurísticas

- **Estrategias clásicas de poda:**
  - Verificar restricciones antes de avanzar
  - Abortar ramas que no pueden mejorar la solución

- **Heurísticas comunes:**
  - Sudoku → elegir primero la celda con menos opciones posibles
  - N-Reinas → ubicar reinas en columnas centrales primero

---

# Cuándo NO usar Backtracking

- **Cuando existe una solución más eficiente**:
  - Programación dinámica (si hay subestructura óptima)
  - Algoritmos greedy (si la propiedad greedy se cumple)
  - Algoritmos específicos del dominio

- **Para problemas con espacios de búsqueda muy grandes** sin buenas podas

- **Cuando solo se necesita una aproximación** (no la solución exacta)

---

# Resumen

**Backtracking** es una técnica algorítmica sistemática para:

- **Explorar** todos los caminos posibles en un espacio de búsqueda

- **Descartar** ramas que no llevan a soluciones (poda)

- **Retroceder** cuando no se puede continuar en un camino

**Características clave:**

- La poda es fundamental para la eficiencia  

- Se usa para encontrar una, todas, o la mejor solución

---

# Preguntas y discusión

- ¿Qué problemas de la materia se pueden resolver con backtracking?
- ¿Cómo identificar oportunidades de poda?
- ¿Cuándo conviene usar backtracking vs. otras técnicas?

---
layout: center
class: text-center
---

# FIN!

<br>

**Backtracking**: Una técnica poderosa para resolver problemas de búsqueda sistemática

<br>

*¿Preguntas?*

