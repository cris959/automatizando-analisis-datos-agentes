## Creación de Herramientas Avanzadas para Agentes de Datos 📊

En esta sesión, expandimos las capacidades de nuestro Agente de IA mediante el desarrollo e integración de **Herramientas Personalizadas (Custom Tools)** utilizando el ecosistema moderno de **LangChain (v0.3+)**. 

El objetivo principal fue automatizar el análisis exploratorio preliminar de un dataset de logística (`datos_entregas.csv`), permitiendo al agente no solo describir los datos de forma textual, sino también generar visualizaciones dinámicas en tiempo real mediante la ejecución controlada de código.

---

## 🛠️ Herramientas Desarrolladas

### 1. Información General del DataFrame (`informaciones_df`)
*   **Propósito:** Proporciona un panorama macro del estado del archivo.
*   **Métricas procesadas:** Dimensiones (`shape`), nombres y tipos de datos de las columnas, conteo de valores nulos, detección de strings de texto `'nan'` ocultos (limpieza de capitalización) y filas duplicadas.
*   **Backend:** Procesa los datos con Pandas, convierte las series resultantes a `string` para cumplir con los contratos de tipado de LangChain, e inyecta la información en un prompt estructurado procesado por el LLM.

### 2. Resumen Estadístico Descriptivo (`resumen_estadistico`)
*   **Propósito:** Genera un informe detallado con métricas de tendencia central y dispersión.
*   **Métricas procesadas:** Transposición del método `.describe(include='number')` (medias, desviaciones estándar, mínimos, máximos y cuartiles).
*   **Backend:** El LLM interpreta la matriz de datos estadísticos para redactar un análisis fluido en Markdown, identificando posibles valores atípicos (*outliers*) y sugiriendo próximos pasos de análisis.

### 3. Generador Dinámico de Gráficos (`generar_grafico`)
*   **Propósito:** Permite al usuario solicitar visualizaciones en lenguaje natural (ej. *"Haz un boxplot del tiempo de entrega por clima"*).
*   **Backend:** 
    *   El LLM recibe los metadatos y una muestra de los datos para escribir **únicamente código Python** basado en `Matplotlib` y `Seaborn`.
    *   Se utiliza el entorno de aislamiento `exec()` para ejecutar el script generado en caliente.
    *   **Optimización de UI:** Se configuraron instrucciones explícitas en el prompt del sistema (`hue` y `legend=False`) para alinearse con los estándares modernos de consistencia de color de **Seaborn v0.13+** y evitar advertencias de depreciación (`FutureWarning`).

---

## ⚙️ Tecnologías y Librerías Utilizadas

*   **Python 3.10+**
*   **Pandas & Matplotlib / Seaborn:** Para la manipulación de datos y renderizado de gráficos.
*   **LangChain Core (v0.3):** Uso de `@tool`, `PromptTemplate` y `StrOutputParser` migrados desde el paquete raíz al núcleo estándar.
*   **IPython Display:** Para el renderizado enriquecido de Markdown en entornos Jupyter/Colab.

---

## 🚀 Aprendizajes Clave de la Clase

1. **Contratos de Tipado en LangChain:** Las herramientas decoradas con `@tool` deben retornar estrictamente cadenas de texto (`str`). Para devolver gráficos, se ejecuta el renderizado en el backend gráfico del cuaderno (`plt.show()`) y se retorna un mensaje de confirmación textual al flujo del Agente.
2. **Inyección Segura de Contexto:** El paso de objetos de Pandas (`Series` o `DataFrames`) hacia las plantillas de prompts requiere una conversión previa explícita (`.to_string()`) para evitar errores de validación de esquemas en los inputs del LLM.
3. **Manejo de Advertencias en Entornos de Producción:** Implementación de filtros de alertas mediante la librería `warnings` de Python para mantener la consola limpia de mensajes de reestructuración interna de librerías terceras.



## 📝 Licencia
Este proyecto está bajo la Licencia MIT. Para más detalles, consulta el archivo [LICENSE](https://github.com/cris959/automatizando-analisis-datos-agentes/blob/main/LICENSE) adjunto en este repositorio.

Copyright © 2026 [Christian Garay](https://github.com//cris959/automatizando-analisis-datos-agentes) - Backend Developer.
