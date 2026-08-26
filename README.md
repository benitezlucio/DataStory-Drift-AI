[README.md](https://github.com/user-attachments/files/31485453/README.md)
# DataStory & Drift AI Engine

**Curso:** Generación de Prompts  
**Estudiante:** Lucio Benitez  
**Comisión:** 96090  
**Entrega:** Preentrega 2 — Fast Prompting en Acción

## 1. Introducción

### Nombre del proyecto
**DataStory & Drift AI Engine: Asistente multimodal para traducir Model Drift y Explicabilidad (XAI) a directivos**

### Presentación del problema
Los modelos de Machine Learning pueden perder rendimiento cuando cambia la distribución de los datos o el comportamiento que intentan representar. El problema no termina en detectar el deterioro: también es necesario comunicarlo de forma comprensible a personas no técnicas que toman decisiones de negocio.

Métricas como PSI, cambios en variables relevantes o explicaciones basadas en SHAP pueden ser útiles para un equipo de Data Science, pero pueden resultar difíciles de interpretar para un comité ejecutivo. Una traducción incorrecta puede generar dos riesgos opuestos: minimizar un deterioro real o exagerarlo con conclusiones que los datos no sostienen.

El proyecto propone reducir esa brecha mediante una POC basada en Fast Prompting que transforma un conjunto controlado de métricas técnicas en:
- un resumen ejecutivo;
- una analogía de negocio;
- un plan de acción;
- riesgos y limitaciones;
- prompts visuales listos para utilizar en una herramienta de generación de imágenes.

La POC no reemplaza al Data Scientist. Su función es acelerar la comunicación y estandarizar el primer borrador, manteniendo una instancia obligatoria de validación humana.

### Desarrollo de la propuesta de solución
La solución combina validaciones determinísticas en Python con un único prompt estructurado para el LLM.

Flujo:

`Métricas -> Validación Python -> Cálculos determinísticos -> Prompt optimizado -> LLM -> Validación de salida -> Revisión humana`

El LLM no calcula métricas críticas ni recibe libertad para inventar información financiera. Los cálculos económicos que puedan resolverse de forma determinística se realizan antes de la consulta y luego se entregan como contexto.

El mismo prompt devuelve tanto la narrativa ejecutiva como dos prompts visuales:
1. **Model Drift:** representación conceptual del cambio de distribución.
2. **XAI:** representación conceptual de cómo una señal técnica se traduce en una explicación comprensible.

De esta manera, la versión de producción de la POC requiere **una sola consulta de texto por caso**.

### Justificación de la viabilidad
La POC es técnicamente viable porque trabaja con estructuras simples: diccionarios de Python, validaciones, plantillas de prompts y una API de texto. No requiere entrenar modelos ni desplegar infraestructura propia.

También es viable desde el punto de vista económico porque:
- la mayor parte del procesamiento se realiza localmente;
- la validación de datos y los cálculos no consumen tokens;
- la salida ejecutiva y los prompts visuales se generan en una única consulta;
- las pruebas comparativas requieren dos consultas únicamente durante la etapa experimental;
- la generación de imágenes puede realizarse manualmente con una herramienta gratuita, evitando una API adicional.

---

## 2. Objetivos

### Objetivo general
Construir una POC en Jupyter Notebook que aplique técnicas de Fast Prompting para traducir señales de Model Drift y XAI a una narrativa ejecutiva clara, trazable y preparada para revisión humana.

### Objetivos específicos
1. Estructurar las métricas técnicas en un formato reutilizable.
2. Comparar un prompt base con un prompt optimizado.
3. Reducir al mínimo la cantidad de consultas a la API.
4. Evitar que el LLM invente datos, relaciones causales o impactos económicos.
5. Incorporar controles de privacidad y validación humana.
6. Generar, dentro de la misma respuesta, prompts visuales para Model Drift y XAI.
7. Documentar costos, limitaciones e iteraciones.

---

## 3. Metodología

### Etapa 1 — Preparación y validación
Se construye un caso sintético de degradación de un modelo de churn. Antes de consultar al LLM, Python valida tipos, rangos y campos sensibles.

### Etapa 2 — Cálculos determinísticos
Los cálculos financieros simples se resuelven con Python. Esto evita pedir al LLM operaciones que pueden realizarse de forma exacta y reduce el riesgo de alucinaciones numéricas.

### Etapa 3 — Experimento de prompting
Se comparan dos configuraciones:
- **Prompt A — Baseline:** instrucción breve, similar a la primera propuesta.
- **Prompt B — Fast Prompting optimizado:** rol + contexto delimitado + reglas de grounding + few-shot + restricciones + formato de salida + auto-chequeo.

La comparación experimental utiliza dos consultas. Una vez seleccionado el prompt optimizado, el flujo normal utiliza una sola.

### Etapa 4 — Validación
La salida se revisa con reglas determinísticas y una rúbrica humana. El sistema marca siempre que la respuesta requiere revisión antes de utilizarse en un comité o decisión de negocio.

### Etapa 5 — Capa multimodal
La respuesta incluye dos prompts visuales. La generación de imágenes se deja como paso manual para evitar una segunda API de pago durante la POC.

---

## 4. Herramientas y tecnologías

- **Python / Jupyter Notebook:** preparación de datos, validaciones y cálculos.
- **OpenAI Responses API:** generación de la narrativa y de los prompts visuales.
- **Pandas:** presentación de tablas de comparación.
- **JSON:** estructura de entrada y salida.
- **NightCafe / Ideogram u otra herramienta gratuita:** evaluación manual de los prompts de imagen.

### Técnicas de Fast Prompting utilizadas

**Role prompting.** Define una perspectiva estable: Lead Data Scientist + Strategy Consultant.

**Context anchoring y delimitadores.** Los datos se entregan dentro de bloques claramente identificados para reducir confusiones entre instrucciones y métricas.

**Few-shot prompting.** Se incorpora un ejemplo breve que muestra cómo traducir una señal técnica a lenguaje ejecutivo sin convertir correlación o importancia en causalidad.

**Task decomposition.** La salida se divide en resumen, analogía, impacto, acciones, limitaciones y prompts visuales.

**Output constraints.** Se exige una estructura JSON y límites de extensión.

**Grounding explícito.** El modelo debe utilizar solamente los datos entregados. Cuando falta información debe declararlo en lugar de completarla.

**Self-check en una sola consulta.** El prompt exige verificar internamente cinco condiciones antes de responder, pero solo devolver el resultado final. Esto evita una consulta adicional de “revisión”.

---

## 5. Controles de seguridad y calidad

### Alucinaciones
- Prohibición explícita de inventar métricas, porcentajes, causas o importes.
- Si falta un dato, la salida debe indicar `NO DISPONIBLE`.
- Los valores financieros llegan calculados desde Python.

### Interpretación de métricas
- PSI se presenta como señal de cambio de distribución bajo una política definida para esta POC, no como una verdad universal.
- SHAP se interpreta como contribución/importancia del modelo y **no como evidencia causal**.
- Caída de precisión no se traduce automáticamente a pérdida monetaria.

### Datos sensibles
La POC bloquea campos asociados a nombres, emails, DNI, cuentas, IDs de clientes y otros identificadores. El ejemplo usa datos sintéticos y agregados.

### Validación humana
La salida incluye siempre `requiere_revision_humana: true`. Ninguna recomendación se considera aprobada para uso ejecutivo sin revisión de una persona responsable de Data Science o del negocio.

---

## 6. Iteraciones concretas de prompts

### Prompt 1 — Texto a texto
**Versión inicial:** pedía explicar PSI, SHAP, impacto financiero y ROI.

**Problema detectado a controlar:** el prompt pedía una “pérdida estimada” sin entregar suficientes variables de negocio, por lo que el modelo podía inventar montos o asumir relaciones que no estaban en los datos.

**Ajuste aplicado:** los montos se calculan en Python y el prompt solo puede narrar valores entregados. Además, se agrega la regla “SHAP no implica causalidad”, un formato JSON y una sección obligatoria de limitaciones.

**Resultado esperado:** un memo ejecutivo breve, accionable y trazable, sin cifras inventadas.

### Prompt 2 — Texto a imagen: Model Drift
**Versión inicial:** priorizaba una estética futurista con gráficos y dashboards.

**Posible problema:** un generador de imágenes puede inventar números, ejes o textos ilegibles que parezcan evidencia real.

**Ajuste aplicado:** se especifica que la imagen debe ser conceptual, sin cifras, sin gráficos cuantitativos y sin etiquetas pequeñas. Se conserva el contraste visual “distribución estable -> distribución desplazada”.

**Resultado esperado:** una imagen de apoyo conceptual que no pueda confundirse con un gráfico analítico real.

### Prompt 3 — Texto a imagen: XAI
**Versión inicial:** representaba una red neuronal dentro de una esfera de vidrio.

**Posible problema:** la estética podía ser demasiado genérica y no comunicar el concepto de explicabilidad.

**Ajuste aplicado:** se pide una composición en tres etapas: señal del modelo -> capa de explicación -> decisión humana, sin afirmar causalidad y sin texto ilegible.

**Resultado esperado:** una metáfora visual clara de la traducción entre modelo y decisión ejecutiva.

> Nota: al ejecutar la POC, conviene guardar capturas u outputs reales de cada versión y reemplazar “resultado esperado” por una observación empírica.

---

## 7. Cronograma

| Semana | Tareas | Producto esperado | Criterio de aprobación |
|---|---|---|---|
| 1 | Preparar caso sintético, definir entradas y validaciones | Dataset/objeto de prueba + validadores | El notebook detecta campos faltantes, rangos inválidos y datos sensibles |
| 2 | Implementar Prompt A y Prompt B | Dos configuraciones comparables | Ambas ejecutan; Prompt B no inventa datos y respeta la estructura |
| 3 | Refinar prompts visuales y controles | Prompts de Model Drift y XAI | No contienen cifras inventadas ni requieren gráficos cuantitativos |
| 4 | Medir llamadas, tokens, documentar y publicar | Notebook + README + repositorio público | El notebook corre de principio a fin y la versión final usa 1 llamada por caso |

---

## 8. Costos y eficiencia

La notebook registra:
- cantidad de consultas;
- tokens de entrada;
- tokens de salida;
- tokens totales.

El experimento A/B utiliza **2 consultas** porque comparar configuraciones forma parte del objetivo académico. Después de elegir el Prompt B, el flujo operativo utiliza **1 consulta por caso**.

No se utiliza una segunda llamada para revisar el texto: el control de consistencia se incorpora dentro del mismo prompt y se complementa con validaciones en Python.

Para evitar gastos innecesarios, la generación de imágenes se mantiene fuera de la API en esta POC.

---

## 9. Implementación

La implementación completa se encuentra en:

`DataStory_Drift_AI_POC.ipynb`

El notebook contiene:
1. configuración;
2. datos sintéticos;
3. validaciones;
4. cálculos determinísticos;
5. Prompt A;
6. Prompt B;
7. ejecución opcional mediante API;
8. comparación;
9. controles de salida;
10. prompts visuales;
11. análisis de costos y conclusiones.

---

## 10. Criterios de éxito de la POC

Se considera exitosa cuando:
- no utiliza PII;
- no inventa cifras;
- diferencia hechos de interpretaciones;
- no presenta SHAP como causalidad;
- genera una narrativa entendible por un perfil ejecutivo;
- entrega un plan de acción sujeto a revisión humana;
- produce prompts visuales reutilizables;
- requiere una sola llamada al LLM en operación normal.

---

## 11. Conclusión

La Preentrega 2 transforma la idea conceptual de DataStory & Drift AI en una POC reproducible. La principal mejora no consiste solamente en escribir un prompt “más detallado”, sino en diseñar un flujo donde Python controla lo determinístico y el LLM se utiliza únicamente para lo que aporta valor: síntesis, traducción y comunicación.

Este enfoque permite mejorar claridad, trazabilidad, seguridad y costo respecto de la propuesta inicial, manteniendo el objetivo original de conectar métricas de Machine Learning con decisiones de negocio.
