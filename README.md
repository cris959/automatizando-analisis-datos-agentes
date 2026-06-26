
## Orquestadores y Agentes ReAct Autónomos
Este módulo está dedicado al desarrollo de agentes autónomos bajo el razonamiento ReAct (Reasoning and Acting) aplicados a la analítica de datos (EDA) sobre nuestro DataFrame de entregas.

## 🥊 La Batalla de las Versiones: El Laberinto de LangChain v0.3
Durante el desarrollo de esta clase, nos enfrentamos a un problema clásico del ecosistema moderno de IA: la volatilidad de las librerías. Con la llegada de LangChain v0.3+, gran parte de los módulos tradicionales de agentes (**create_react_agent**, **initialize_agent**, **AgentType**) fueron deprecados, fragmentados o migrados por completo hacia arquitecturas externas como LangGraph.

Intentar importar los métodos estándar generaba constantes excepciones en el entorno:

* **ImportError: cannot import name 'create_react_agent' from 'langchain.agents'**

* **ModuleNotFoundError: No module named 'langchain.agents.react'**

## 🛠️ La Solución: El Código "Bypass" Nativo
Para evitar depender de las constantes actualizaciones y cambios de nombres en los paquetes de LangChain, implementamos un orquestador ReAct nativo y personalizado. Este código actúa como un bypass que toma el control del bucle de pensamiento y acción utilizando Python puro, garantizando la estabilidad total del cuaderno.

El Código de Ejecución Independiente
````
Python
class MiOrquestadorReAct:
    def __init__(self, llm, tools, prompt_template):
        self.llm = llm
        # Mapeamos las herramientas por su nombre para llamarlas dinámicamente
        self.tools = {t.name: t for t in tools}
        self.prompt_template = prompt_template

    def invoke(self, inputs):
        pregunta = inputs["input"]
        # Formateamos las herramientas en formato texto para el LLM
        tools_str = "\n".join([f"{t.name}: {t.description}" for t in self.tools.values()])
        tool_names_str = ", ".join(self.tools.keys())
        
        scratchpad = ""
        max_pasos = 5
        
        print(f"🤔 Pregunta inicial: {pregunta}\n" + "="*50)
        
        for paso in range(max_pasos):
            # 1. Armamos el prompt con el historial actual (scratchpad)
            prompt_final = self.prompt_template.format(
                input=pregunta,
                agent_scratchpad=scratchpad,
                tools=tools_str,
                tool_names=tool_names_str
            )
            
            # 2. Le pedimos al LLM que piense el siguiente paso
            respuesta_llm = self.llm.invoke(prompt_final).content
            print(respuesta_llm)
            print("-" * 50)
            
            # Sumamos la respuesta del LLM a la memoria temporal
            scratchpad += respuesta_llm
            
            # 3. Validamos si el agente ya llegó a la respuesta final
            if "Final Answer:" in respuesta_llm:
                resultado_final = respuesta_llm.split("Final Answer:")[-1].strip()
                return {"output": resultado_final}
            
            # 4. Si no terminó, buscamos qué herramienta quiere ejecutar
            try:
                lineas = respuesta_llm.split("\n")
                action = [l for l in lineas if "Action:" in l][0].split("Action:")[-1].strip()
                action_input = [l for l in lineas if "Action Input:" in l][0].split("Action Input:")[-1].strip()
                
                # Limpiamos posibles comillas que el LLM ponga por error
                action_input = action_input.strip("'\"")
                
                if action in self.tools:
                    print(f"⚙️ [Ejecutando herramienta] {action} con entrada: {action_input}")
                    # Ejecutamos la herramienta nativa
                    observacion = self.tools[action].invoke(action_input)
                    print(f"👁️ [Observación]: {observacion}\n" + "="*50)
                    
                    # Le alimentamos el resultado al prompt del agente para el próximo paso
                    scratchpad += f"\nObservation: {observacion}\nThought: "
                else:
                    observacion = f"Error: La herramienta '{action}' no existe."
                    scratchpad += f"\nObservation: {observacion}\nThought: "
                    
            except Exception as e:
                observacion = f"Error al parsear el formato ReAct. Asegúrate de incluir obligatóriamente 'Action:' y 'Action Input:' en líneas separadas."
                scratchpad += f"\nObservation: {observacion}\nThought: "

        return {"output": "Se alcanzó el límite máximo de pasos sin obtener una respuesta final."}
````

## 🎯 Capacidades Logradas en esta Clase
Con este enfoque independiente, el agente logró resolver con éxito tres pilares analíticos:

1- Consultas de estructura: Inspeccionar datos crudos interpretando esquemas del DataFrame.

2- Estadística Descriptiva: Invocar herramientas de cálculo estadístico (**.describe()**) de manera automatizada.

3- Graficación Dinámica en Caliente: Escribir y ejecutar scripts interactivos de **matplotlib** y **seaborn** delegando la ejecución al entorno interactivo de Python para renderizar los resultados visuales en tiempo real.

## 📝 Licencia
Este proyecto está bajo la Licencia MIT. Para más detalles, consulta el archivo [LICENSE](https://github.com/cris959/automatizando-analisis-datos-agentes/blob/main/LICENSE) adjunto en este repositorio.

Copyright © 2026 [Christian Garay](https://github.com//cris959/automatizando-analisis-datos-agentes) - Backend Developer.
