# Estructuras de Datos Tabulares en Java

Este repositorio contiene el diseño e implementación desde cero de una biblioteca nativa en Java para la manipulación, limpieza y análisis de datos tabulares bidimensionales. El proyecto fue desarrollado para la materia Algoritmos 1 de la Licenciatura en Ciencia de Datos (UNSAM), bajo la estricta restricción de **no utilizar dependencias ni librerías externas**.

El objetivo central de la arquitectura fue garantizar un sistema fuertemente tipado, modular y extensible que minimice el acoplamiento ante futuras modificaciones estructurales.

## 📊 Características Principales

* **Engine de Datos Nativo:** Estructuras de datos eficientes para representar filas, columnas y celdas individuales sin librerías de terceros.
* **Manejo de Tipos Fuertemente Tipado:** Implementación de polimorfismo y genéricos para soportar tipos de datos homogéneos por columna (`Integer`, `Double`, `Boolean`, `String`) y gestión explícita de valores faltantes (`CeldaNA`).
* **Pipelines de Procesamiento Dinámico:** Módulo de auto-casteo capaz de inferir y procesar el tipo de dato predominante en una columna durante la lectura de archivos CSV.
* **Estrategias de Imputación (Data Cleaning):** Interfaz extensible para el tratamiento de valores nulos o faltantes (`NA`), permitiendo aplicar técnicas de sustitución basadas en el tipo de columna.
* **Módulo de Consultas Avanzadas:** Filtrado declarativo mediante el uso de predicados funcionales (`Predicate<Object>`), selección indexada, ordenamiento de filas por múltiples columnas, operaciones de concatenación y muestreo estocástico.

---

## 📐 Decisiones de Diseño y Arquitectura

### 1. Jerarquía de Componentes Homogéneos
Para mitigar la rigidez de la herencia tradicional, el motor se estructuró a través de un componente base unificado:
* `Celda<T>`: Clase abstracta genérica que encapsula el valor primitivo. Sus implementaciones concretas (`CeldaNumber`, `CeldaString`, etc.) encapsulan las reglas de parsing y comparación profunda (`copiaProfunda` y `equals`).
* `ArrayCelda`: Estructura contenedora base que actúa como el backend de almacenamiento para filas y columnas.
* `Columna<T>` y `Fila`: Extensiones especializadas que heredan de `ArrayCelda`, implementando validación de tipos en tiempo de inserción para evitar la corrupción de datos en la estructura tabular.

### 2. Matriz Extensible de Excepciones (`ExcepcionTabla`)
Se diseñó un subsistema de control de fallas con excepciones personalizadas de tipo `RuntimeException`. Esto garantiza la resiliencia del motor ante:
* Inconsistencias de formato o columnas inválidas al procesar archivos CSV.
* Intentos de inserción de tipos de datos no válidos en columnas homogéneas.
* Desbordamiento de rangos o accesos indexados fuera de los límites permitidos.

---

## 📂 Estructura del Proyecto

```text
└── src/
    ├── Celda/
    │   ├── Celda.java              # Abstracción genérica de almacenamiento
    │   ├── CeldaNumber.java        # Manejo avanzado de tipos numéricos (Float/Double/Long)
    │   ├── CeldaBoolean.java       # Encapsulamiento lógico
    │   ├── CeldaString.java        # Encapsulamiento de texto
    │   └── CeldaNA.java            # Representación polimórfica de valores nulos (Missing Data)
    ├── Array/
    │   ├── ArrayCelda.java         # Backend de almacenamiento unificado
    │   ├── Fila.java               # Abstracción y operaciones de filas
    │   └── Columnas/
    │       ├── Columna.java        # Validador y contenedor de tipos por columna
    │       └── [Typed]Columna.java # Columnas concretas (Number, String, Boolean, NA)
    ├── ExcepcionTabla/
    │   └── *.java                  # Matriz jerárquica de excepciones del dominio
    ├── util/
    │   └── ImputarFaltantes.java   # Contrato para algoritmos de limpieza de datos
    ├── Casteo.java                 # Engine de inferencia de tipos y procesamiento masivo
    ├── Filtro.java                 # Componente desacoplado para evaluación de predicados funcionales
    └── Demo.java                   # Orquestador del ciclo de vida y simulación integral
