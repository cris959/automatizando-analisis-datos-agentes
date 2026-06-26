## Creación de un Agente Autónomo de Análisis de Datos con LangChain y Groq 📊🤖

Este módulo contiene la implementación paso a paso de un agente inteligente capaz de traducir consultas en lenguaje natural a código ejecutable de **Python / Pandas** para analizar un conjunto de datos, ejecutándolo de forma automática en un entorno aislado.

---

## 🛠️ Stack Tecnológico Utilizado
*   **Orquestador:** LangChain (`0.3.x`)
*   **LLM (Modelo de Lenguaje):** `llama-3.3-70b-versatile` vía **Groq API**
*   **Análisis de Datos:** Python 3 & Pandas
*   **Motor de Ejecución:** `PythonAstREPLTool` (LangChain Experimental)

---

## 🚀 Conceptos y Flujo Desarrollado

Durante la clase automatizamos el flujo completo de análisis mediante una arquitectura de **Tool Calling** (Llamada a herramientas) utilizando **LCEL (LangChain Expression Language)**:

1.  **Inyección del Contexto (Prompt Dinámico):** El sistema lee dinámicamente el esquema del DataFrame (`df.columns.to_list()`) y se lo inyecta al prompt del sistema para que el modelo conozca la estructura exacta de los datos.
2.  **Mapeo y Vinculación de Herramientas (`bind_tools`):** Adaptamos el motor interactivo REPL tradicional envolviéndolo con el decorador `@tool`, forzando al modelo de Groq a responder estrictamente estructurando argumentos para nuestra función.
3.  **Procesamiento de la Salida (`JsonOutputKeyToolsParser`):** Implementamos un parser para limpiar la respuesta del LLM, extrayendo de forma automatizada únicamente la cadena de código Python generada.
4.  **Ejecución Autónoma (`RunnableLambda`):** Conectamos el bloque final de la cadena para alimentar el motor AST, ejecutando el script directamente en la memoria del cuaderno de Colab y retornando el resultado numérico o estadístico.

---

## 📂 Ejemplos de Consultas Resueltas por el Agente

El agente es capaz de interpretar variables y resolver problemas complejos de manera autónoma, tales como:

*   **Cálculo de Correlaciones de Pearson:**
    *   *Pregunta:* "¿Cuál es la correlación entre años experiencia del colaborador y tiempo de entrega?"
    *   *Código generado por la IA:* `df['años_experiencia_colaborador'].corr(df['tiempo_entrega'])`
*   **Agrupaciones y Promedios Estadísticos:**
    *   *Pregunta:* "Calcula el promedio de tiempo de entrega para cada clima?"
    *   *Código generado por la IA:* `df.groupby('clima')['tiempo_entrega'].mean()`

---

## 💻 Estructura Final de la Cadena (LCEL)

El flujo automatizado quedó consolidado en la siguiente arquitectura de código:

```python
from langchain_core.runnables import RunnableLambda
from langchain_core.output_parsers.openai_tools import JsonOutputKeyToolsParser

# Configuración del Parser y del Ejecutor en caliente
parser = JsonOutputKeyToolsParser(key_name="ejecutar_codigo_python", first_tool_only=True)
ejecutor = RunnableLambda(lambda inputs: repl_tool.invoke(inputs["codigo"]))

# Súper cadena automatizada (Chain)
cadena_completa = prompt | llm_con_herramienta | parser | ejecutor

# Invocación directa
respuesta = cadena_completa.invoke({"question": "Tu pregunta aquí"})
print(respuesta)
```
💡 Nota: Este cuaderno fue subido al repositorio con las celdas limpias de salidas (outputs) redundantes para mantener las buenas prácticas de desarrollo backend.



## 📝 Licencia
Este proyecto está bajo la Licencia MIT. Para más detalles, consulta el archivo [LICENSE](https://github.com/cris959/automatizando-analisis-datos-agentes/blob/main/LICENSE) adjunto en este repositorio.

Copyright © 2026 [Christian Garay](https://github.com//cris959/automatizando-analisis-datos-agentes) - Backend Developer.
