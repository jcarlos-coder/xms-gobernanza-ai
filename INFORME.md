# Informe: hacia un desarrollo asistido por IA estructurado

> Este informe propone un conjunto de prácticas, reglas y herramientas para
> llevar nuestro flujo de trabajo con IA de algo intuitivo a algo gobernado,
> trazable y predecible. No parte de un diagnóstico de errores del equipo —
> parte de un patrón que la industria entera está discutiendo ahora mismo, y
> de la oportunidad de adelantarnos a él aprovechando lo que ya tenemos
> invertido en herramientas.

## 1. Vibe coding vs. software estructurado

**Vibe coding** es el nombre que le está dando la industria a un patrón
concreto: aceptar lo que una IA genera mayormente por intuición, sin un
proceso que verifique, documente o deje registro de por qué se tomó una
decisión. No es un defecto de ningún equipo en particular — es lo que pasa
por defecto cuando una IA puede producir código plausible en segundos y
nada reemplaza la fricción que antes forzaba una segunda mirada.

El riesgo no es que la IA no sirva. Es que, sin estructura, los costos se
esconden: un atajo que nadie registró, una suposición que ya estaba
desactualizada, un fallo que se parchea sin diagnosticar la causa real. Esos
costos no se ven el primer día — se acumulan y aparecen todos juntos más
adelante, cuando ya son caros.

**Software estructurado**, tal como lo plantea este informe, no significa
más lento ni más burocrático. Significa que cuatro cosas pasan siempre, sin
que nadie tenga que acordarse de pedirlas: se verifica antes de construir, se
registra todo atajo tomado a propósito, se diagnostica antes de volver a
parchear, y nada se degrada en silencio.

## 2. Gobernanza

Gobernanza, en este contexto, es simple: **quién decide qué, cuándo se
revisa un cambio, y qué puede tocar la IA sin supervisión directa.** No es
una capa de burocracia nueva — es la diferencia entre que una decisión
importante quede en la cabeza de una persona o quede en un proceso que el
equipo entero puede confiar en que se cumple, se haya acordado de revisarlo
o no.

Hoy, buena parte de esto ya existe en las herramientas que usamos (ver
sección 5) — lo que falta es la regla que fuerza su uso consistente, no la
herramienta en sí.

## 3. Reglas de agente — `development-rules.md`

Este kit incluye 14 reglas que se cargan automáticamente en cada sesión de
IA, sin que nadie tenga que invocarlas — por diseño, para que una tarea sin
ninguna palabra clave especial ("arreglá este detalle") no se quede afuera de
la disciplina.

Las reglas se agrupan en cuatro ejes:

- **Construir solo lo necesario, y bien:** ¿esto hace falta? ¿ya existe algo
  que lo resuelve? Nunca recortar validación/seguridad para ahorrar líneas.
- **No confiar ciegamente en uno mismo:** ni en la memoria de la IA sobre una
  tecnología (puede estar desactualizada), ni en una investigación recién
  hecha (puede ser la decisión equivocada aunque esté bien fundamentada).
- **Mantener el proyecto honesto y recuperable:** deuda técnica con contrato
  formal, no implícita; documentación que no puede mentir; evidencia de
  tests que vive dentro del repo, no se pierde en `/tmp`.
- **Fallar en voz alta, nunca en silencio:** si una herramienta o memoria
  falla, se declara — nunca se sigue de largo como si nada.

Un dato relevante sobre cómo se construyeron: cada una de las 14 reglas fue
puesta a prueba antes de aceptarse — leída en frío, sin contexto, por dos
modelos de IA de capacidad distinta (uno chico, uno grande), contra casos
concretos, específicamente para encontrar reglas que suenan bien pero se
malinterpretan en la práctica. Varias se reescribieron más de una vez por
esto. No es un documento de intenciones — es un documento verificado.

## 4. Los cuatro ejes del cambio

### a. Control de código

Qué puede tocar la IA sin supervisión, y qué no. Hoy ya tenemos TDD, un
flujo de spec/diseño antes de codificar (ver sección 7), y control de
versiones — lo que faltaba era el límite explícito: la IA nunca toca
secretos de producción, configuración de CI/CD, ni hace push directo a una
rama protegida sin que una persona apruebe ESE cambio puntual. Todo lo demás
sigue el proceso normal de PR/review del equipo.

### b. Gobernanza

Quién revisa qué y cuándo. El sistema de revisión por capas que ya tenemos
(riesgo, resiliencia, legibilidad, confiabilidad) se activa de forma
proporcional al riesgo real del cambio — no todo cambio necesita el mismo
nivel de revisión, y forzarlo sería la misma sobre-ingeniería que las reglas
de este kit evitan en el código.

### c. Optimización (de tiempo y tokens, no solo de gasto)

El desperdicio real no es "gastar tokens" — es parchear a ciegas sin
encontrar la causa de un fallo, entrar en un ciclo de prueba-y-error que
nunca converge. La regla nueva de este kit lo ataca directo: la primera vez
que un fallo (o una variante cercana) reaparece después de un fix, eso es la
señal de diagnosticar la causa real, no de aplicar otro parche.

### d. Seguimiento (trazabilidad)

Por qué se construyó algo, y qué tarea o spec lo justifica. Las
herramientas para esto ya existen (roadmap, memoria persistente, el flujo de
spec) — lo que falta es la regla que las hace de uso obligatorio, no
opcional cuando a alguien se le ocurre.

## 5. Herramientas sugeridas

Sin entrar en el detalle técnico — eso vive en el `README.md` de este mismo
kit, con descripciones y enlaces de descarga de cada una:

- **Runtime de IA** (Claude Code / OpenCode) — el entorno donde todo esto
  corre.
- **gentle-ai** — la capa que configura memoria, flujo de spec, y revisión
  sobre el runtime.
- **Engram** — memoria persistente entre sesiones, para no perder el "por
  qué" de una decisión una vez que termina la conversación que la tomó.
- **CodeGraph** — un mapa indexado del código, para que la IA responda "quién
  llama a esto" sin tener que adivinar leyendo todo el repo.

## 6. Arquitectura de software en los proyectos

Se propone un estándar de arquitectura consistente entre proyectos, no como
mandato cerrado sino como punto de partida a discutir con cada equipo técnico:

- **Arquitectura limpia / hexagonal:** la lógica de negocio no depende de un
  framework, una base de datos o una UI específica — esas son piezas
  intercambiables (adaptadores) alrededor de un núcleo estable.
- **Screaming Architecture:** la estructura de carpetas organizada por
  dominio/funcionalidad, no por capa técnica — que al abrir el repo se vea
  qué hace el producto, no qué framework usa.
- **Atomic design / patrón contenedor-presentación:** separar qué-se-ve de
  cómo-se-comporta en la UI. La regla 4 de nuestro propio set ya fuerza esto
  a nivel de archivo (markup en un lado, lógica en otro) — este punto lo
  eleva a estándar de proyecto completo, no solo de componente.

## 7. TDD, ODD y RDD — resumen y motivo

**TDD (Test-Driven Development):** escribir el test antes que el código que
lo pasa — rojo, verde, refactor. Por qué: obliga a pensar el contrato antes
de la implementación, y detecta una regresión antes de que llegue a
producción, no después.

**ODD (Organic-Driven Development):** el flujo de trabajo que ya corre en
nuestro setup — explorar el problema en proporción a su tamaño, trackear el
trabajo sustancial automáticamente (sin ceremonia para lo chico), implementar
tarea por tarea, cerrar con el resultado verificado. Por qué: mantiene el
trabajo hecho con IA auditable y recuperable sin forzar un proceso pesado de
spec para cada cambio mínimo.

**RDD (Receipt-Driven Development):** la revisión automática que corre sobre
un cambio antes de entregarlo, proporcional al riesgo real (un cambio
trivial no dispara la misma revisión que uno de alto riesgo). Por qué: agrega
una revisión adversarial y consistente encima del output de la IA, antes de
que llegue a una persona que puede estar apurada o cansada ese día — sin
convertir cada cambio chico en un proceso pesado.

**El motivo común a los tres:** ninguno depende de que alguien se acuerde de
aplicarlo. Igual que las 14 reglas de la sección 3, funcionan por defecto,
no por disciplina individual de cada persona del equipo en cada momento.
