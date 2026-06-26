## Implementación de Agentes Nativos de LangChain para Data Science 📊🤖

Este módulo documenta la evolución desde las cadenas manuales construidas con LCEL hacia la implementación de arquitecturas de agentes de alto nivel utilizando **`create_pandas_dataframe_agent`**. El sistema permite interactuar de forma autónoma con estructuras de datos complejas, resolviendo consultas múltiples y avanzadas mediante el uso nativo de *Tool Calling*.

---

## 🛠️ Stack Tecnológico Utilizado
* **Framework Principal:** LangChain (`0.3.x`)
* **Módulo Experimental:** `langchain_experimental` (Agentes especializados)
* **LLM (Modelo de Lenguaje):** `llama-3.3-70b-versatile` (u homólogos con soporte nativo de herramientas) vía **Groq API**
* **Análisis y Manipulación de Datos:** Python 3 & Pandas

---

## 🚀 Conceptos y Capacidades Implementadas

Durante esta clase, automatizamos el análisis de datos estructurados abstrayendo la lógica de bajo nivel mediante un agente empaquetado y robusto:

1.  **Agente de Alto Nivel (`create_pandas_dataframe_agent`):** Implementamos el agente nativo especializado en Pandas, el cual configura de forma interna el prompt del sistema, las herramientas de ejecución de código y el bucle de razonamiento (ReAct / Tool Calling).
2.  **Seguridad en la Ejecución (`allow_dangerous_code=True`):** Configuramos explícitamente el entorno para permitir la ejecución de código dinámico generado por el LLM en el entorno local (REPL), cumpliendo con los estándares modernos de seguridad del framework.
3.  **Monitoreo del Razonamiento (`verbose=True`):** Activamos el modo detallado para auditar en consola el flujo de pensamiento del agente (*Thought*), el script exacto de Pandas que decide generar y la respuesta cruda del intérprete.
4.  **Procesamiento de Preguntas Compuestas y Secuenciales:** Guiamos al agente para resolver problemas analíticos de múltiples pasos (por ejemplo, calcular correlaciones cruzadas o extraer dimensiones y tipos de datos en una misma corrida) sin caer en interrupciones tempranas (*early stopping*).
5.  **Control de Entrada con `inspect.cleandoc`:** Optimizamos la inyección de prompts eliminando espacios en blanco innecesarios y tabulaciones heredadas de las triples comillas de Python, garantizando un input limpio y forzando al modelo a responder estrictamente en español.

---

## 📂 Ejemplos de Consultas Avanzadas Resueltas

El agente es capaz de interpretar variables correlacionadas y analizar la estructura del DataFrame de forma 100% autónoma:

* **Análisis de Correlación Avanzada (Pregunta Compuesta):**
    * *Consulta:* ¿Cuál es la correlación entre los años de experiencia del colaborador y el tiempo de entrega? ¿Es mayor que la correlación entre la clasificación del colaborador y el tiempo de entrega?
    * *Comportamiento del Agente:* Genera secuencialmente los métodos `.corr()` de Pandas para ambas variables, compara los coeficientes y redacta una conclusión analítica.
* **Auditoría de Estructura (Acciones Secuenciales):**
    * *Consulta:* ¿Cuáles son las dimensiones del dataframe? ¿Cuáles son los nombres de las columnas y los tipos de datos?
    * *Comportamiento del Agente:* Combina comandos como `.shape` y `.dtypes` para mapear la matriz de datos por completo.

---

## 💻 Configuración de Código Base

La arquitectura final del agente quedó consolidada bajo el siguiente bloque de inicialización:

```python
import inspect
from langchain_experimental.agents import create_pandas_dataframe_agent

# 1. Configuración e instanciación del agente ejecutor
agente_ejecutor = create_pandas_dataframe_agent(
    llm=llm,
    df=df,
    agent_type='tool-calling',
    verbose=True,
    allow_dangerous_code=True
)

# 2. Formateo limpio del prompt para el usuario
pregunta_analitica = inspect.cleandoc("""
    Realiza las siguientes acciones sobre el dataframe de forma secuencial:
    1. Obtén las dimensiones exactas (filas y columnas).
    2. Obtén los nombres de todas las columnas junto con sus tipos de datos.
    
    Responde detalladamente con ambos resultados estrictamente en español.
""")

# 3. Invocación autónoma
respuesta = agente_ejecutor.invoke({"input": pregunta_analitica})
```
💡 Nota: Al igual que en los módulos anteriores, el cuaderno se mantiene limpio de outputs redundantes o errores de validación de Pydantic, siguiendo las buenas prácticas de desarrollo backend.

## 📝 Licencia
Este proyecto está bajo la Licencia MIT. Para más detalles, consulta el archivo [LICENSE](https://github.com/cris959/automatizando-analisis-datos-agentes/blob/main/LICENSE) adjunto en este repositorio.

Copyright © 2026 [Christian Garay](https://github.com//cris959/automatizando-analisis-datos-agentes) - Backend Developer.
