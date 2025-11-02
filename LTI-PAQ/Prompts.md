--- PROMPT #1 

#Rol
Te consulto como un afamado y experimentado product manager, donde has desarrollado toda tu carrera profesional en el área de Recursos Humanos de muchas multinacionales, en el país y el mundo entero.

#Contexto
Necesitamos desarrollar un sistema ATS, donde debemos encontrar el mayor valor agregado posible a todo lo que se desarrolló hasta el presente, aprovechando lo mejor de cada software conocido, y agregado todo aquello que uno aspira contemplar y aún no se ha desarrollado
entre nuestros principales objetivos debemos contemplar : aumentar la eficiencia para los departamentos de HR, mejorar la colaboración en tiempo real entre reclutadores y managers, automatizaciones, asistencia de IA en diversas tareas

#Objetivo de la tarea
en esta primera etapa, vamos a tener que cumplir los siguientes objetivos.
1) Descripción breve del software LTI, valor añadido y ventajas competitivas. Explicación de las funciones principales. Añadir un diagrama Lean Canvas para entender el modelo de negocio
2) Descripción de los 3 casos de uso principales, con el diagrama asociado a cada uno
3) Modelo de datos que cubra entidades, atributos (nombre y tipo) y relaciones
4) Diseño del sistema a alto nivel, tanto explicado como diagrama adjunto
5) Diagrama C4 que llegue en profundidad a uno de los componentes del sistema, el que prefieras

#Tarea
en este primer paso, es elaborar simplemente un plan de trabajo para aclarar todas las tareas que debemos desarrollar en forma ordenada para cumplir con todos los objetivos mencionados.
ahora te titulado de las diferentes etapas que vamos a realizar, para que, a continuación, defininamos paso a paso cada una de ellas (como una especie de historia de usuario)

#Ejemplo
sin entrar a condicionar ni orientar tu trabajo, un ejemplo de la primer respuesta que espero sería.
- definir tipos de usuarios alcanzados por el sistema
- definir tareas que debe contemplar el sistema
- definir sobre qué sistemas existentes en el mercado obtendremos ideas en cuanto a fortalezas y debilidades de cada uno
- definir valores agregados a aplicar al sistema
- diseñar la base de datos
- diseñar los casos de uso
- diseñar el diagrama C4
- etc etc.

#Otras consideraciones
Preguntame cualquier duda que te surja para el desarrollo, en cualquiera de las etapas que vayamos desarrollando a lo largo de este trabajo.

--- Prompt #2

Sospecho que mi ejemplo te sesgó para el análisis de esta tarea, te pido que lo vuelvas a pensar y ver si no consigues superar la propuesta, o sigues considerando que este plan es el más eficiente para alcanzar los resultados obtenidos. al finalizar, vuelve a reiterar las dudas que aún mantienes para poder responderte.

--- Prompt #3

Procedo a responder tus inquietudes.
Segmento objetivo inicial : consideramos que será para enterprise multinacionales, de toda latinoamérica
Stack y restricciones tecnológicas : Operaremos sobre servidor Azure, SO Windows Server, base de datos MS SQL server enterprise, y lenguajes web que consideres más apropiado, tanto para backend como frontend
Integraciones imprescindibles en el MVP : consideraremos todo aquello que has mencionado (calendarios, correo, LinkedIn/bolsas, HRIS actual, e-sign).
Políticas de datos: encriptación de los datos más sensibles de los postulantes, solución vía web, anonimización en las etapas de análisis de postulantes para evitar sesgamiento, 
Componente para C4 deep-dive : priorizamos Workflow orchestrator
Disponibilidad de usuarios para tests (recruiters/managers) y si podemos filmar sesiones para insights : consideramos la mayor disponibilidad posible y NO filmar las sesiones

--- Prompt #4

Respondo nuevas dudas.
Idiomas previstos: en esta primera versión, sólo en español.
Integración HRIS primaria: no está definido cuál es el sistema core
Tipo de PoC IA preferida para la etapa 2: resumen automático de entrevistas
Grado de anonimización esperado: aplicar técnicas más avanzadas.
¿Deseás incluir dashboard analítico (Power BI embebido o similar) dentro del MVP o dejarlo para etapa siguiente : lo dejamos para etapa siguiente.

ahora con todas las dudas resueltas, comencemos por desarrollar el primer sprint

--- Prompts siguientes.

{se fue aprobando cada etapa de presentación y autorizando a avanzar a la siguiente etapa}

--- Prompt

quiero ya pasar al paquete final, con todos los entregables... tienes en claro los 5 puntos del objetivo? querés recordarlos antes de armar el paquete final?
