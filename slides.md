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

<div class="overflow-y-auto max-h-96">

```cpp
void backtracking(Estado& estado) {
    // Poda opcional: evita explorar ramas inútiles
    if (hayQuePodar(estado)) {
        return;
    }

    // Caso base: verificar si encontramos una solución
    if (esSolucion(estado)) {
        procesarSolucion(estado);
        return;
    }

    // Caso recursivo: explorar todas las posibilidades
    for (Movimiento mov : posiblesMovimientos(estado)) {
        if (movimientoValido(estado, mov)) {
            hacerMovimiento(estado, mov);      // Avanzar
            backtracking(estado);              // Recursión
            deshacerMovimiento(estado, mov);   // Retroceder
        }
    }
}
```

</div>

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

**Problemas típicos donde es útil:**

- 🏰 **N-Reinas** - Colocación sin conflictos
- 🐴 **Caballo** - Caminos óptimos en tablero
- 🧩 **Sudoku** - Completar con restricciones
- 📝 **Subconjuntos/Permutaciones** - Generación sistemática
- 🧭 **Laberintos** - Encontrar salidas y caminos

---

# Ejemplo: N-Reinas

**Objetivo**: Colocar N reinas en un tablero de NxN sin que se ataquen entre sí.

**Estrategia**:

- Colocar una reina por columna
- Verificar que no haya conflictos con reinas anteriores
- Backtrack si no se puede colocar en ninguna fila

--- 

# Código de N-Reinas

<div class="overflow-y-auto max-h-80 text-xs">

```cpp
#include "ListAdy.cpp"
#include <cassert>
#include <iostream>
#include <limits>
#include <string>
using namespace std;

bool esSolucion(int c, int n) { return c == n; }
void imprimirSolucion(int n, bool **reinas) {
  for (int f = 0; f < n; f++) {
    for (int c = 0; c < n; c++) {
      cout << "[";
      if (reinas[f][c]) {
        cout << "R";
      } else {
        cout << " ";
      }
      cout << "] ";
    }
    cout << endl;
  }
  cout << endl << endl;
}

bool esCoordenadaValida(int f, int c, int n) {
  return f >= 0 && c >= 0 && f < n && c < n;
}

bool puedoAplicarMovimiento(int filaCand, int colCand, int n,
                            bool **candidata) {
  // chequeo la horizontal
  int f = filaCand;
  int c = colCand;
  while (esCoordenadaValida(f, c, n)) {
    if (candidata[f][c]) {
      return false;
    }
    c--;
  }
  // chequea arriba - izquierda
  f = filaCand;
  c = colCand;
  while (esCoordenadaValida(f, c, n)) {
    if (candidata[f][c]) {
      return false;
    }
    c--;
    f--;
  }
  // chequea abajo - izquierda
  f = filaCand;
  c = colCand;
  while (esCoordenadaValida(f, c, n)) {
    if (candidata[f][c]) {
      return false;
    }
    c--;
    f++;
  }
  return true;
}

void aplicarMovimiento(int f, int c, bool **candidata) {
  candidata[f][c] = true;

}
void deshacerMovimiento(int f, int c, bool **candidata) {
  candidata[f][c] = false;
}

void n_reinas_bt(int colAc, int n, bool **candidata, int &cantSoluciones) {
  if (esSolucion(colAc, n)) {
    cantSoluciones++;
    // imprimirSolucion(n, candidata);
  } else {
    for (int filaAc = 0; filaAc < n; filaAc++) {
      if (puedoAplicarMovimiento(filaAc, colAc, n, candidata)) {
        aplicarMovimiento(filaAc, colAc, candidata);
        n_reinas_bt(colAc + 1, n, candidata, cantSoluciones);
        deshacerMovimiento(filaAc, colAc, candidata);
      }
    }
  }
}

void n_reinas_una_bt(int colAc, int n, bool **candidata, bool &exito) {
  if (!exito) {
    if (esSolucion(colAc, n)) {
      exito = true;
      imprimirSolucion(n, candidata);
    } else {
      for (int filaAc = 0; filaAc < n; filaAc++) {
        if (puedoAplicarMovimiento(filaAc, colAc, n, candidata)) {
          aplicarMovimiento(filaAc, colAc, candidata);
          n_reinas_una_bt(colAc + 1, n, candidata, exito);
          deshacerMovimiento(filaAc, colAc, candidata);
        }
      }
    }
  }
}

int n_reinas(int n) {
  bool **candidata = new bool *[n]();
  int cantSol = 0;
  for (int i = 0; i < n; i++) {
    candidata[i] = new bool[n]();
  }
  n_reinas_bt(0, n, candidata, cantSol);
  return cantSol;
}

void n_reinas_una(int n) {
  bool **candidata = new bool *[n]();
  for (int i = 0; i < n; i++) {
    candidata[i] = new bool[n]();
  }
  bool exito = false;
  n_reinas_una_bt(0, n, candidata, exito);
}
```

</div>

---

# Ejemplo: Caballo

**Objetivo**: Encontrar el camino más corto para que un caballo de ajedrez llegue de una casilla inicial a una destino.

**Estrategia**:

- Explorar los 8 movimientos posibles del caballo
- Usar poda para evitar caminos más largos que el mejor encontrado
- No repetir casillas visitadas

--- 

# Código de Caballo

<div class="overflow-y-auto max-h-80 text-xs">

```cpp
#include "ListImp.cpp"
#include <cassert>
#include <iostream>
#include <limits>
#include <string>
using namespace std;

#define N 8

bool esSolucion(int fAc, int cAc, int fDe, int cDes) {
  return fAc == fDe && cAc == cDes;
}

void imprimirSolucion(int **camino) {
  for (int f = 0; f < N; f++) {
    for (int c = 0; c < N; c++) {
      cout << "[";
      if (camino[f][c] == -1) {
        cout << "  ";
      } else if (camino[f][c] < 10) {
        cout << " " << camino[f][c];
      } else {
        cout << camino[f][c];
      }
      cout << "] ";
    }
    cout << endl;
  }
}

bool esCoordenadaValida(int f, int c) {
  return f < N && c < N && f >= 0 && c >= 0;
}

bool puedoAplicarMovimiento(int f, int c, int **candidata) {
  return esCoordenadaValida(f, c) &&
         candidata[f][c] == -1; // si es una cordenada valida y no pase
}

void aplicarMovimiento(int fCand, int cCand, int pasoActual,
                       int **caminoCandidato) {
  caminoCandidato[fCand][cCand] = pasoActual;
}

void deshacerMovimiento(int fCand, int cCand, int **caminoCandidato) {
  caminoCandidato[fCand][cCand] = -1;
}

bool esMejorSolucion(int pasosActual, int mejoresPasos) {
  return pasosActual < mejoresPasos;
}

bool esMejorOIgualSolucion(int pasosActual, int mejoresPasos) {
  return pasosActual <= mejoresPasos;
}

bool puedoPodar(int actual, int mejor) { return actual > mejor; }

int **clonarSolucion(int **origen) {
  int **duplicado = new int *[N]();
  for (int f = 0; f < N; f++) {
    duplicado[f] = new int[N]();
    for (int c = 0; c < N; c++) {
      duplicado[f][c] = origen[f][c];
    }
  }
  return duplicado;
}

void caballo_bt(int fAc, int cAc, int fDe, int cDe, int **caminoCandidato,
                int &mejorPasos, ListImp<int **> *&mejoresCaminos) {
  if (!puedoPodar(caminoCandidato[fAc][cAc], mejorPasos)) {
    if (esSolucion(fAc, cAc, fDe, cDe) &&
        esMejorOIgualSolucion(caminoCandidato[fAc][cAc], mejorPasos)) {
      if (esMejorSolucion(caminoCandidato[fAc][cAc], mejorPasos)) {
        mejoresCaminos->clear();
      }
      mejoresCaminos->insert(clonarSolucion(caminoCandidato));
      mejorPasos = caminoCandidato[fAc][cAc];
    } else {
      int dFila[8] = {2, 1, -1, -2, -2, -1, 2, 1};
      int dCol[8] = {1, 2, -2, -1, 1, 2, -1, -2};
      int pasoActual = caminoCandidato[fAc][cAc];
      for (int mov = 0; mov < 8; mov++) {
        int fCand = fAc + dFila[mov];
        int cCand = cAc + dCol[mov];

        if (puedoAplicarMovimiento(fCand, cCand, caminoCandidato)) {
          aplicarMovimiento(fCand, cCand, pasoActual + 1, caminoCandidato);
          caballo_bt(fCand, cCand, fDe, cDe, caminoCandidato, mejorPasos,
                     mejoresCaminos);
          deshacerMovimiento(fCand, cCand, caminoCandidato);
        }
      }
    }
  }
}

void caballo(int fAc, int cAc, int fDe, int cDe) {
  int **candidata = new int *[N]();
  ListImp<int **> *mejoresSoluciones = new ListImp<int **>();
  for (int f = 0; f < N; f++) {
    candidata[f] = new int[N]();
    for (int c = 0; c < N; c++) {
      candidata[f][c] = -1;
    }
  }

  candidata[fAc][cAc] = 1;
  int mejoresPasos = INT_MAX;
  caballo_bt(fAc, cAc, fDe, cDe, candidata, mejoresPasos, mejoresSoluciones);
  for (int i = 0; i < mejoresSoluciones->getSize(); i++) {
    int **unaMejorSol = mejoresSoluciones->get(i);
    cout << "una nueva solucion a todos tus problemas llego:" << endl;
    imprimirSolucion(unaMejorSol);
    cout << endl << endl;
  }
  cout << "en total se han encontrado " << mejoresSoluciones->getSize()
       << " soluciones" << endl;
}
```

</div>

---

# Ejemplo: Problema de la Mochila

**Objetivo**: Buscar el máximo valor posible sin exceder la capacidad.

**Estrategia**:

- Para cada objeto: decidir si incluirlo o no
- Explorar ambas opciones recursivamente
- Mantener el mejor valor encontrado

--- 

# Código de la Mochila 0-1
 
<div class="overflow-y-auto max-h-80 text-xs">

```cpp
#include <cassert>
#include <climits>
#include <iostream>
#include <limits>
#include <string>
using namespace std;

int cantObjetos = 4;
string nombres[4] = {"ruby", "lingote", "moneda", "diamante"};
int valores[4] = {10, 7, 5, 20};
int pesos[4] = {5, 6, 2, 3};

bool puedoPodar() {
  return false; // claro que no esto es una optimizacion hacia arriba
}

bool esSolucion(int objIndex) {
  return objIndex == cantObjetos; // termine de evaluar todos los objetos
}

bool esMejorSolucion(int actual, int mejor) { return actual > mejor; }

void clonarSolucion(bool *origen, bool *destino) {
  for (int i = 0; i < cantObjetos; i++) {
    destino[i] = origen[i];
  }
}

bool puedoUsarObjeto(int capacidad, int objIndex) {
  return capacidad >= pesos[objIndex];
}

void aplicarMovimiento(int &capacidad, bool *&objetosUsados, int &valorMochila,
                       int objIndex) {
  capacidad -= pesos[objIndex];
  objetosUsados[objIndex] = true;
  valorMochila += valores[objIndex];
}

void deshacerMovimiento(int &capacidad, bool *&objetosUsados, int &valorMochila,
                        int objIndex) {
  capacidad += pesos[objIndex];
  objetosUsados[objIndex] = false;
  valorMochila -= valores[objIndex];
}

void mochila_bt(int capacidad, int objIndex, bool *objetosUsados,
                int valorActualMochila, bool *&mejorObjetosUsados,
                int &mejorValorMochila) {
  if (!puedoPodar()) {
    if (esSolucion(objIndex)) {
      if (esMejorSolucion(valorActualMochila, mejorValorMochila)) {
        mejorValorMochila = valorActualMochila;
        clonarSolucion(objetosUsados, mejorObjetosUsados);
      }
    } else {
      for (int uso = 0; uso <= 1; uso++) {
        if (uso) {
          if (puedoUsarObjeto(capacidad, objIndex)) {
            aplicarMovimiento(capacidad, objetosUsados, valorActualMochila,
                              objIndex);
            mochila_bt(capacidad, objIndex + 1, objetosUsados,
                       valorActualMochila, mejorObjetosUsados,
                       mejorValorMochila);
            deshacerMovimiento(capacidad, objetosUsados, valorActualMochila,
                               objIndex);
          }
        } else {
          mochila_bt(capacidad, objIndex + 1, objetosUsados, valorActualMochila,
                     mejorObjetosUsados, mejorValorMochila);
        }
      }
    }
  }
}

void mochila_01(int capacidad) {
  bool *usados = new bool[cantObjetos]();
  bool *mejorUsados = new bool[cantObjetos]();
  int mejorValorMochila = -1;
  mochila_bt(capacidad, 0, usados, 0, mejorUsados, mejorValorMochila);
  cout << mejorValorMochila << endl;
}
```

</div>

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

**Complejidad típica**: Los problemas resueltos con backtracking suelen tener complejidad factorial o exponencial.

**Factores que afectan el rendimiento**:

- 📏 Tamaño del espacio de búsqueda
- ✂️ Calidad de las podas implementadas
- 🔄 Orden en que se prueban las opciones

> **Clave**: Backtracking no "vence" la complejidad exponencial, pero permite resolver problemas de tamaño moderado de forma práctica.

---

# Poda Avanzada y Heurísticas

- **Estrategias clásicas de poda:**

  - Verificar restricciones antes de avanzar
  - Abortar ramas que no pueden mejorar la solución

- **Heurísticas comunes:**
  - Sudoku → elegir primero la celda con menos opciones posibles
  - Caballo → priorizar movimientos que lleven a casillas con menos salidas

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

<div class="flex flex-col items-center justify-center h-full">
  <h1>FIN!</h1>
  <p><b>Backtracking:</b> Una técnica poderosa para resolver problemas de búsqueda sistemática</p>
  <p><i>¿Preguntas?</i></p>
</div>
