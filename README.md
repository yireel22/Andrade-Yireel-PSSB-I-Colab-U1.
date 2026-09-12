

---

```markdown
# 🧠 Programación de Sistemas de Base I — Unidad I
### Inspección de la Estructura de un Compilador y Procesamiento de Lenguajes

[![Python](https://img.shields.io/badge/Python-3.13-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![GCC](https://img.shields.io/badge/GCC-11.4-A42E2B?style=for-the-badge&logo=gnu&logoColor=white)](https://gcc.gnu.org/)
[![Colab](https://img.shields.io/badge/Google-Colab-F9AB00?style=for-the-badge&logo=googlecolab&logoColor=white)](https://colab.research.google.com/)
[![License](https://img.shields.io/badge/License-Académica-blue?style=for-the-badge)](#-licencia)

> **Asignatura:** Programación de Sistemas de Base I (8.º Semestre)  
> **Unidad I:** Introducción a la Compilación  
> **Autor:** Yireel Andrade  
> **Repositorio:** `Andrade-Yireel-PSSB-I-Colab-U1.`  
> **Tiempo estimado:** 3 horas (Asíncrono / Guiado)

---

## 📖 Descripción General

Este repositorio contiene el **cuaderno interactivo de Google Colab** correspondiente a la **Unidad I** de la asignatura *Programación de Sistemas de Base I*. El objetivo central es **inspeccionar de forma tangible la estructura interna de un compilador**, contrastando el pipeline de compilación de **C (GCC)** con el modelo híbrido de **Python (intérprete + bytecode)**.

A lo largo del notebook se exploran las fases del **Front-End** de un compilador —análisis léxico, sintáctico, semántico y generación de código intermedio— utilizando herramientas nativas del entorno Linux de Colab (`gcc`, `python3`, `dis`, `ast`, `tokenize`).

---

## 🎯 Objetivos de Aprendizaje

1. **Diferenciar** de forma tangible la ejecución **compilada** (C) frente a la **interpretada/híbrida** (Python) usando `gcc`, `python3`, `dis` y `ast`.
2. **Explorar y visualizar** las fases de **Análisis Léxico y Sintáctico** mediante la inspección del **Árbol de Sintaxis Abstracta (AST)** y la generación de **Bytecode**.
3. **Comprender** el rol de la **Tabla de Símbolos** en el seguimiento de identificadores durante el proceso de traducción.

---

## 🗂️ Estructura del Notebook

| Sección | Contenido |
|---------|-----------|
| **1. Objetivos** | Metas de aprendizaje de la unidad |
| **2. Conexión Teórica (NotebookLM)** | Preguntas de verificación: fases del Front-End, lexema vs. token, Tabla de Símbolos |
| **3. Práctica 1** | Pipeline de Compilación vs. Interpretación (CLI / Bash) con GCC |
| **4. Práctica 2** | Revelando el Front-End: Análisis Léxico y AST en Python |
| **5. Práctica 3** | Inspección de la Tabla de Símbolos y Bytecode |
| **6. Desafío U1** | Entregable: Especificación y prototipo del lenguaje propio |

---

## 🧪 Sección Práctica 1 — Pipeline de Compilación vs. Interpretación

Se analiza cómo el sistema operativo procesa un lenguaje **compilado (C)** frente a uno **interpretado (Python)**.

### Flujo completo de compilación con GCC

| Fase | Comando | Artefacto |
|------|---------|-----------|
| Preprocesador | `gcc -E hola_compilador.c -o hola_compilador.i` | `.i` |
| Representación Intermedia (GIMPLE) | `gcc -O2 -fdump-tree-gimple ...` | `.c.*gimple` |
| Ensamblador | `gcc -S hola_compilador.c -o hola_compilador.s` | `.s` |
| Código Objeto | `gcc -c hola_compilador.c -o hola_compilador.o` | `.o` |
| Enlace Dinámico | `gcc hola_compilador.c -o hola_compilador` | ejecutable |
| Enlace Estático | `gcc -static hola_compilador.c -o hola_estatico` | ejecutable (182,934 líneas) |

> 💡 **Dato clave:** El binario con enlace dinámico usa la **PLT (Procedure Linkage Table)** para resolver `printf` en tiempo de ejecución, mientras que el estático inyecta **miles de líneas** de código de `libc` directamente en el ejecutable.

---

## 🔬 Sección Práctica 2 — Front-End: Análisis Léxico y AST

### Tokenización en Python

Para el código fuente:

```python
suma = a + 10
```

El tokenizador produce:

| Línea/Col | Tipo de Token | Valor (Lexema) |
|-----------|---------------|----------------|
| 1:0 | `NAME` | `suma` |
| 1:5 | `OP` | `=` |
| 1:7 | `NAME` | `a` |
| 1:9 | `OP` | `+` |
| 1:11 | `NUMBER` | `10` |

### Árbol de Sintaxis Abstracta (AST)

```python
Module(
    body=[
        Assign(
            targets=[Name(id='suma', ctx=Store())],
            value=BinOp(
                left=Name(id='a', ctx=Load()),
                op=Add(),
                right=Constant(value=10)))])
```

---

## 📊 Sección Práctica 3 — Tabla de Símbolos y Bytecode

### Simulación de Tabla de Símbolos

Se implementó una clase `TablaDeSimbolos` que registra:

| NOMBRE | TIPO | ÁMBITO | VALOR |
|--------|------|--------|-------|
| `a` | ENTERO | global | 5 |
| `b` | ENTERO | global | 10 |
| `suma` | ENTERO | global | 15 |

### Bytecode de Python (Máquina de Pila)

```python
def calcular():
    a = 5
    b = 10
    suma = a + b
    return suma
```

Desensamblado con `dis`:

```
RESUME                   0
LOAD_CONST               1 (5)
STORE_FAST               0 (a)
LOAD_CONST               2 (10)
STORE_FAST               1 (b)
LOAD_FAST_LOAD_FAST      1 (a, b)
BINARY_OP                0 (+)
STORE_FAST               2 (suma)
LOAD_FAST                2 (suma)
RETURN_VALUE
```

---

## 🚀 Sección de Trabajo No Dirigido — Prototipo de Inspección

Se implementó un **Inspector Léxico completo** en Python que:

- ✅ Tokeniza código fuente con `tokenize`.
- ✅ Realiza **análisis léxico manual** con expresiones regulares.
- ✅ **Compara** ambos métodos y reporta diferencias.
- ✅ Clasifica tokens: palabras reservadas, identificadores, literales, operadores.
- ✅ Genera un **reporte de tokens** (`reporte_tokens.txt`).

### 🏗️ Tabla de Símbolos Avanzada

Se construyó una **Tabla de Símbolos profesional** con **25 atributos** organizados en 9 categorías:

| Categoría | Atributos | Cantidad |
|-----------|-----------|----------|
| Identificación | Nombre, Tipo de símbolo | 2 |
| Tipo de dato | Tipo, Anotación | 2 |
| Valor | Valor actual, Inicial, Constancia | 3 |
| Ámbito | Visibilidad, Padre, Anidamiento | 3 |
| Memoria | Dirección, Offset, Tamaño | 3 |
| Ubicación | Línea, Columna, Archivo | 3 |
| Uso | Veces usado, Líneas, Modificación, Última modificación | 4 |
| Documentación | Docstring, Etiquetas | 2 |
| Relaciones | Dependencias, Parámetros, Retorno | 3 |
| **TOTAL** | | **25** |

---

## 📁 Archivos Generados

| Archivo | Descripción | Tamaño |
|---------|-------------|--------|
| `hola_compilador.c` | Código fuente en C | ~400 B |
| `hola_compilador.i` | Código preprocesado | — |
| `hola_compilador.s` | Ensamblador (AT&T) | — |
| `hola_compilador_8664.s` | Ensamblador (Intel) | — |
| `hola_compilador.o` | Código objeto | — |
| `hola_estatico` | Ejecutable con enlace estático | 182,934 líneas |
| `codigo_python_original.py` | Código Python de prueba | 1,638 B |
| `reporte_tokens.txt` | Reporte de análisis léxico | 19,724 B |
| `tabla_simbolos.txt` | Tabla de símbolos completa | 10,882 B |

---

## 🧰 Tecnologías Utilizadas

- ![Python](https://img.shields.io/badge/Python-3.13-3776AB?logo=python&logoColor=white) — Intérprete y análisis con `ast`, `dis`, `tokenize`
- ![C](https://img.shields.io/badge/C-GCC_11.4-A42E2B?logo=c&logoColor=white) — Compilación nativa y análisis de pipeline
- ![Bash](https://img.shields.io/badge/Bash-Linux-4EAA25?logo=gnubash&logoColor=white) — Automatización de comandos
- ![Colab](https://img.shields.io/badge/Google-Colab-F9AB00?logo=googlecolab&logoColor=white) — Entorno de ejecución

---

## 📚 Conexión Teórica — Conceptos Clave

### Fases del Front-End de un Compilador

1. **Análisis Léxico** — Convierte caracteres en tokens.
2. **Análisis Sintáctico** — Construye el árbol sintáctico.
3. **Análisis Semántico** — Verifica tipos y coherencia.
4. **Generación de Código Intermedio** — Produce representación de bajo nivel.

### Lexema vs. Token

| Concepto | Definición | Ejemplo |
|----------|------------|---------|
| **Lexema** | Secuencia concreta de caracteres | `"cuenta"`, `31` |
| **Token** | Categoría léxica abstracta | `ID`, `NUM` |

> 🔑 **Analogía:** El token es la categoría *"fruta"*; el lexema es *"manzana"*, *"plátano"*, etc.

### Tabla de Símbolos

Estructura de datos que actúa como **diccionario central** del compilador. Almacena:

- Tipo de dato
- Dirección de memoria
- Ámbito (scope)
- Dimensiones de arreglos
- Parámetros de funciones

Se implementa típicamente con **tablas hash** para búsquedas en tiempo ~O(1).

---

## 🎓 Desafío U1 — Producto Integrador

### Parte A: Documento de Especificación (`ESPECIFICACION.docx`)

1. **Nombre del Lenguaje Original y Propósito**
2. **Ejemplo de Código Fuente Válido** (10-15 líneas)
3. **Catálogo Preliminar de Tokens**

### Parte B: Prototipo de Inspección en Código

1. Crear archivo con ejemplo de código fuente original.
2. Implementar función de tokenización.
3. Mostrar **Tabla de Símbolos inicial**.

### ✅ Checklist de Viabilidad

- [ ] Al menos **3 tipos de tokens** diferenciados
- [ ] Soporte para **expresiones aritméticas/lógicas anidadas**
- [ ] Al menos **una estructura de control de flujo**
- [ ] Mecanismo explícito de **asignación/declaración**

---

## 🚦 Cómo Ejecutar

1. Abre el notebook en **Google Colab**:
   ```
   https://colab.research.google.com/drive/1SQ5lI9h9ygtYZ9CpdsD_VJZTulnZo1fL
   ```
2. Ejecuta las celdas **en orden secuencial**.
3. Los archivos generados aparecerán en el panel de archivos de Colab.
4. Guarda una copia en tu GitHub personal:
   - `Archivo` → `Guardar una copia en GitHub`

---

## 📝 Autoevaluación

### ❓ Pregunta 1 — Conflictos Léxicos

> *¿Existe ambigüedad entre palabras reservadas e identificadores en Python?*

**Respuesta:** No. Python resuelve la ambigüedad mediante:
1. **Tokenización** — Genera tokens `NAME` para todas las palabras.
2. **Contexto** — El parser clasifica según la posición gramatical.
3. **Gramática** — Define dónde pueden aparecer las palabras reservadas.
4. **Validación** — Verifica que las reservadas estén en contexto válido.

### ❓ Pregunta 2 — Estructura de la Tabla de Símbolos

> *¿Qué atributos necesita la Tabla de Símbolos?*

**Respuesta:** 25 atributos en 9 categorías (ver tabla arriba). Cada atributo cumple una función específica:

| Atributo | ¿Para qué sirve? | Consecuencia si falta |
|----------|------------------|----------------------|
| Nombre | Identificar el símbolo | No se puede referenciar |
| Tipo de dato | Verificar operaciones | Errores de tipo |
| Ámbito | Controlar visibilidad | Conflictos de nombres |
| Dirección memoria | Generar código máquina | No se puede asignar memoria |

---

## 📦 Entregables

1. ✅ Cuaderno de Colab con todas las salidas ejecutadas
2. ✅ Documento `ELYirs_Documento de propuesta.docx` con la especificación del lenguaje
3. ✅ Registro en **Moodle** con los enlaces correspondientes

---

## 👤 Autor

**Yireel Andrade**  
Estudiante de 8.º Semestre — Programación de Sistemas de Base I  
Repositorio: [`Andrade-Yireel-PSSB-I-Colab-U1.`](https://github.com/yireel22/Andrade-Yireel-PSSB-I-Colab-U1.)

---

## 📄 Licencia

Este proyecto es de **uso académico**. El código y documentación aquí presentados son parte del **Portafolio de Evidencias** de la asignatura *Programación de Sistemas de Base I*.

---

<div align="center">

**⭐ Si este repositorio te fue útil, considera darle una estrella ⭐**

Hecho con 💻 y ☕ para la clase de **PSSB I**

</div>
```

---
