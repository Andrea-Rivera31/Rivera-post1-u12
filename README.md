
# Computación Emergente y Tendencias: Circuitos Cuánticos con Qiskit 1.x
## Arquitectura de Computadores — Unidad 12: Post-Contenido 1

---

**Asignatura:** Arquitectura de Computadores  
**Programa:** Ingeniería de Sistemas  
**Universidad:** Universidad Francisco de Paula Santander (UFPS)  
**Año:** 2026  

## Datos del Estudiante
* **Nombre:** Andrea Valentina Rivera Fernández  
* **Código:** 1152444

---

## Descripción del Laboratorio
Este repositorio contiene el informe técnico y el código fuente correspondiente a la implementación, simulación y análisis de circuitos cuánticos utilizando el framework de código abierto de nivel industrial **Qiskit 1.x** e **IBM Qiskit Aer**. 

A través de este componente práctico, se exploran y validan empíricamente tres pilares fundamentales de la mecánica cuántica aplicada a la informática:
1. **Generación de correlaciones no locales máximas** mediante la construcción de un estado entrelazado de Bell.
2. **Demostración de la ventaja cuántica determinista** frente a la complejidad computacional clásica a través del algoritmo de Deutsch-Jozsa.
3. **Optimización del espacio de búsqueda** en bases de datos no estructuradas mediante la amplitud geométrica del algoritmo de Grover en un registro de 2 qubits.

Las ejecuciones de los circuitos se modelan localmente en el procesador mediante el motor de simulación clásica de alto rendimiento `AerSimulator`, sin requerir llamadas remotas a la infraestructura cuántica de IBM Quantum Cloud.

---

## Especificaciones del Entorno de Desarrollo y Prerrequisitos
Para garantizar la reproducibilidad de los experimentos, el entorno local de ejecución en arquitectura macOS se configuró bajo los siguientes parámetros técnicos:

* **Lenguaje de Programación:** Python 3.9 o superior (`python3 --version`)
* **Framework Cuántico:** Qiskit 1.x (Paquetes principales: `qiskit`, `qiskit-aer`, `matplotlib`)
* **Aislamiento de Dependencias:** Entorno virtual nativo de Python (`quantum_env`)

---

## Estructura Absoluta del Proyecto

En cumplimiento estricto con las directrices normativas de la guía de la UFPS, el repositorio estructurado bajo la raíz con nomenclatura oficial `rivera-post1-u12` presenta la siguiente jerarquía de archivos:

```text
rivera-post1-u12/
├── capturas/                  # Evidencias gráficas e histogramas de probabilidad
│   ├── bell_histogram.png     # Histograma del experimento del Estado de Bell
│   ├── grover_00.png          # Histograma de Grover: Búsqueda del objetivo 00
│   ├── grover_01.png          # Histograma de Grover: Búsqueda del objetivo 01
│   ├── grover_10.png          # Histograma de Grover: Búsqueda del objetivo 10
│   └── grover_11.png          # Histograma de Grover: Búsqueda del objetivo 11
├── src/                       # Código fuente modular en Python/Qiskit
│   ├── bell_state.py          # Script de generación del Estado de Bell
│   ├── deutsch_jozsa.py       # Script de ejecución del algoritmo Deutsch-Jozsa
│   └── grover.py              # Script de la búsqueda acelerada de Grover
└── README.md                  # Reporte técnico formal institucional (Este archivo)

```

---

## Análisis Técnico e Interpretación de los Experimentos

### 1. Estado de Bell — Entrelazamiento Cuántico (`src/bell_state.py`)

El circuito tiene como objetivo preparar el estado entrelazado más simple conocido como el estado de Bell:


$$\lvert\Phi^{+}\rangle = \frac{\lvert00\rangle + \lvert11\rangle}{\sqrt{2}}$$

#### Diagrama del Circuito (Representación ASCII):

```text
     ┌───┐      ┌─┐   
q_0: ┤ H ├──■───┤M├───
     └───┘┌─┴─┐ └╥┘┌─┐
q_1: ─────┤ X ├──╫─┤M├
          └───┘  ║ └╥┘
c: 2/════════════╩══╬═
                 0  1

```

#### Justificación Mecánico-Cuántica:

* **Superposición Uniforme:** Los registros se inicializan por defecto en el estado base ket $\lvert00\rangle$. Al aplicar una **puerta Hadamard ($H$)** sobre el qubit `q_0`, su estado transiciona a una superposición equiprobable de bases computacionales: $\lvert+\rangle = \frac{\lvert0\rangle + \lvert1\rangle}{\sqrt{2}}$. En este punto, el estado global del sistema es separable: $\lvert\psi\rangle = \frac{\lvert00\rangle + \lvert10\rangle}{\sqrt{2}}$.
* **Entrelazamiento Máximo:** Al aplicar una **puerta de No-Controlado (CNOT / $CX$)** tomando a `q_0` como línea de control y a `q_1` como objetivo, se entrelazan linealmente los estados de ambas partículas. Si `q_0` computa en $\lvert0\rangle$, `q_1` permanece inalterado en $\lvert0\rangle$; si `q_0` computa en $\lvert1\rangle$, `q_1` se invierte a $\lvert1\rangle$. El sistema resultante es algebraicamente no separable, lo que significa que el estado de un qubit no puede describirse de forma independiente al del otro.
* **Colapso Estadístico:** Tras evaluar **1024 disparos (shots)** en el simulador, el sistema colapsa con una distribución estadística aproximada de **50% para $\lvert00\rangle$ y 50% para $\lvert11\rangle$**. Las amplitudes de los estados cruzados $\lvert01\rangle$ y $\lvert10\rangle$ registran una probabilidad matemática nula ($0.0\%$), demostrando una correlación cuántica perfecta e instantánea (acción fantasmal a distancia).

---

### 2. Algoritmo de Deutsch-Jozsa (`src/deutsch_jozsa.py`)

Este algoritmo resuelve de manera determinista un problema clásico de caja negra: clasificar si una función oculta (oráculo) $f: \{0,1\}^n \rightarrow \{0,1\}$ es **Constante** (retorna el mismo valor para cualquier entrada) o **Balanceada** (retorna `0` para exactamente la mitad del espacio de entradas y `1` para la otra mitad).

#### Comparativa de Complejidad Computacional ($n = 2$ qubits):

* **Caso Clásico (Peor Escenario):** Con $n=2$, existen $2^2 = 4$ posibles entradas computacionales (`00`, `01`, `10`, `11`). Un ordenador clásico requiere evaluar en el peor de los casos $2^{n-1} + 1 = 3$ consultas individuales al oráculo. Si las dos primeras consultas devuelven el mismo bit (ej. `0`), una tercera consulta es indispensable para validar si la función mantiene la uniformidad (constante) o cambia (balanceada).
* **Caso Cuántico:** Se resuelve el problema empleando **exactamente 1 consulta al oráculo**.

#### Dinámica de Interferencia y Retroceso de Fase (*Phase Kickback*):

1. Se añade un qubit auxiliar (*ancilla*) inicializado en $\lvert1\rangle$ y transformándolo mediante una puerta $H$ en el estado anti-simétrico:

$$\lvert-\rangle = \frac{\lvert0\rangle - \lvert1\rangle}{\sqrt{2}}$$


2. Al evaluar el oráculo sobre los qubits de datos en superposición uniforme, la salida de la función no altera los bits directamente, sino que se "patea" o retroalimenta hacia atrás en forma de una fase compleja geométrica $(-1)^{f(x)}$ sobre las amplitudes del registro principal.
3. La capa de compuertas Hadamard final ejecuta una transformación de Fourier que provoca una **interferencia cuántica**:
* Si la función es **Constante**, las fases interfieren de manera constructiva concentrando el $100\%$ de la densidad de probabilidad en el estado origen $\lvert00\rangle$.
* Si la función es **Balanceada**, la interferencia destruye la amplitud del estado origen, garantizando que la medición devuelva de manera determinista cualquier estado excitado ortogonal (distinto a `00`).



---

### 3. Algoritmo de Grover en 2 Qubits (`src/grover.py`)

El algoritmo ejecuta una búsqueda dentro de una base de datos desordenada de $N = 2^n = 4$ elementos con una aceleración cuadrática de orden $\mathcal{O}(\sqrt{N})$.

#### Justificación Matemática de la Eficiencia (1 Iteración Óptima):

Para un espacio de búsqueda acotado de 2 qubits ($n=2$, $N=4$), el número óptimo de iteraciones cuánticas ($R$) necesarias para maximizar la amplitud de probabilidad del estado marcado se modela geométricamente mediante la rotación de vectores en el plano espacial de Grover:


$$R \le \frac{\pi}{4}\sqrt{N} = \frac{\pi}{4}\sqrt{4} = \frac{\pi}{2} \approx 1.57 \implies R_{\text{óptimo}} = 1 \text{ iteración}$$

El vector de estado inicial arranca con un ángulo de proyección $\theta$ respecto al plano de los estados no marcados, calculado como:


$$\sin(\theta) = \frac{1}{\sqrt{N}} = \frac{1}{\sqrt{4}} = \frac{1}{2} \implies \theta = 30^\circ$$

Cada iteración de Grover aplica un operador oráculo (que invierte la fase del estado objetivo con un signo negativo) seguido de un operador de difusión (que refleja las amplitudes vectoriales alrededor de la media del sistema). Este proceso rota geométricamente el vector de estado hacia el objetivo un ángulo neto de $2\theta = 60^\circ$ por cada ciclo.

Por ende, tras concluir **exactamente 1 iteración**, la posición angular final acumulada del vector es:


$$\theta_{\text{final}} = 30^\circ + 60^\circ = 90^\circ$$

Dado que un ángulo de $90^\circ$ se alinea de forma colineal y perfecta sobre el vector del estado objetivo marcado, la probabilidad teórica ideal de colapso en la medición es de $\sin^2(90^\circ) = 1.0 \ (100\%)$.

---

## Matriz Analítica y Reporte de Resultados de la Simulación

A continuación, se tabula de manera estructurada el comportamiento del sistema simulado tras realizar un muestreo de **1024 disparos (shots)** por cada experimento independiente:

| Experimento / Algoritmo | Estado Objetivo Solicitado | Estado con Máxima Probabilidad | Conteo Obtenido (Shots) | Porcentaje de Certeza | Interpretación Cuántica del Resultado | Estado del Test |
| :--- | :---: | :---: | :---: | :---: | :--- | :---: |
| **Paso 1: Estado de Bell** | $\lvert\Phi^{+}\rangle$ | $\lvert00\rangle$<br>$\lvert11\rangle$ | 514<br>510 | 50.2%<br>49.8% | Correlación cuántica perfecta. Los estados de error $\lvert01\rangle$ y $\lvert10\rangle$ reportan un valor absoluto de cero. | **OK** |
| **Paso 2: Deutsch-Jozsa** | Constante | $\lvert00\rangle$ | 1024 | 100.0% | Interferencia destructiva sobre estados excitados. Clasificación correcta de la función constante. | **OK** |
| **Paso 2: Deutsch-Jozsa** | Balanceado | $\lvert11\rangle$ | 1024 | 100.0% | Ausencia determinista del estado origen $\lvert00\rangle$. Clasificación correcta de la función balanceada. | **OK** |
| **Paso 3: Grover** | `00` | $\lvert00\rangle$ | 1024 | 100.0% | Amplificación geométrica perfecta de la amplitud. Coincidencia exacta con el target. | **CORRECTO** |
| **Paso 3: Grover** | `01` | $\lvert10\rangle$ | 1024 | 100.0% | **Discrepancia detectada:** Inversión en el mapeo de bits debido al orden de la arquitectura de Qiskit. | <span style="color:red">**ERROR**</span> |
| **Paso 3: Grover** | `10` | $\lvert01\rangle$ | 1024 | 100.0% | **Discrepancia detectada:** Inversión en el mapeo de bits debido al orden de la arquitectura de Qiskit. | <span style="color:red">**ERROR**</span> |
| **Paso 3: Grover** | `11` | $\lvert11\rangle$ | 1024 | 100.0% | Amplificación geométrica perfecta. Al ser un estado simétrico, la inversión de bits es invariante. | **CORRECTO** |


### Diagnóstico de Ingeniería Cuántica (Análisis de Errores en Grover):

Al evaluar de manera automatizada los estados asimétricos `01` y `10`, el sistema arrojó un estado de validación de **`ERROR`**. El simulador arrojó con un $100\%$ de certeza los estados inversos (`10` y `01` respectivamente).

**Explicación Científica:** Este comportamiento no se debe a una falla física en el operador de difusión ni a un error en el simulador clásico `AerSimulator`. Responde a una discrepancia metodológica estructural: **Qiskit utiliza de forma nativa la convención de ordenamiento de bits "Little-Endian"** (donde el qubit $q_0$ representa el bit menos significativo y se posiciona a la derecha de la cadena de texto: $\lvert q_1 q_0\rangle$).

Los condicionales del código fuente original evaluaron las cadenas de entrada bajo la lógica clásica "Big-Endian", provocando que las compuertas de inversión de fase $X$ se aplicaran de manera cruzada sobre los qubits inversos en los escenarios asimétricos, provocando la lectura reflejada en la terminal.

---

## Conclusiones del Laboratorio

1. **Validación del Entrelazamiento Cuántico:** El experimento del Estado de Bell demostró que el entrelazamiento no es una probabilidad clásica compartida, sino una interconexión donde el estado de un qubit determina instantáneamente el del otro. Esto se evidenció al obtener un 0% de probabilidad en los estados cruzados $\lvert01\rangle$ y $\lvert10\rangle$.


2. **Demostración de la Ventaja Cuántica:** El algoritmo de Deutsch-Jozsa comprobó la superioridad del procesamiento cuántico al reducir a **exactamente 1 consulta** un problema que a un ordenador clásico le tomaría hasta 3 consultas en el peor de los casos. Esto demuestra la utilidad práctica de la superposición y el *phase kickback*.


3. **Eficiencia en la Amplificación de Amplitud:** A través del algoritmo de Grover, se validó matemáticamente cómo el operador de difusión y el oráculo logran rotar el vector de estado exactamente $60^\circ$ por iteración. Para un espacio de 2 qubits ($N=4$), esto permite alcanzar el objetivo al 100% de probabilidad teórica en una única iteración, logrando la aceleración cuadrática esperada.


4. **Impacto de la Arquitectura del Software:** El análisis de los errores en los estados `01` y `10` permitió comprender la importancia de las convenciones de ordenamiento de bits (Little-Endian vs. Big-Endian) al programar sobre frameworks reales como Qiskit 1.x, destacando que el diseño de un circuito debe alinearse siempre con la arquitectura del simulador o hardware utilizado.

---

