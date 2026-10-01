# Índice de Entrega — Primer Examen Parcial
## Desarrollo de Aplicaciones Móviles Nativas

**Institución:** Instituto Politécnico Nacional — Escuela Superior de Cómputo (ESCOM)  
**Plan de Estudios:** Ingeniería en Sistemas Computacionales (Plan 2020)  
**Materia:** Desarrollo de Aplicaciones Móviles Nativas  
**Periodo:** 2027-1 | **Grupo:** 7CV4  
**Profesor:** Hurtado Avilés Gabriel (`gabrielhuav`)  
**Alumno:** Aragón Martínez Manuel Alejandro  
**Usuario de GitHub:** `ManuelAAM`  
**Repositorio Base:** [gabrielhuav/PolitecnicoOpenWorld](https://github.com/gabrielhuav/PolitecnicoOpenWorld/)  
**Repositorio Fork:** [ManuelAAM/PolitecnicoOpenWorld](https://github.com/ManuelAAM/PolitecnicoOpenWorld)  
**Rama de Trabajo:** `feature-update-asset-tutorialmundoabierto`  
**SHA Base de Referencia:** `7ed32539`  
**Versión de la Aplicación:** 1.0.0.18  
**Fecha de Entrega:** 1 de octubre de 2026  

---

## 1. Resumen Ejecutivo y Delimitación del Cambio

### 1.1 Título de la Contribución
**Responsive Layout, Vertical Scroll and Gesture Swipe Navigation in Open World and Interior Controls Tutorial**  
*(Adaptabilidad Responsive, Desplazamiento Vertical y Navegación por Gestos Táctiles en el Tutorial de Controles de Mundo Abierto e Interiores)*

### 1.2 Fenómeno Descubierto y Problemática (El "Antes")
Al ejecutar el modo **Mundo Libre (pre-alpha)** o ingresar a **Ajustes → Controles → Tutorial · Mundo Abierto / Tutorial · Interiores**, la aplicación despliega una ventana emergente (`ControlsTutorialOverlay`) que orienta al jugador sobre las mecánicas y botones del juego.

En dispositivos operando en **orientación horizontal (landscape)** —como el **Samsung Galaxy A54 5G** (resolución 2340×1080 px, altura de viewport libre de ~360dp a 400dp)— o con **fuentes del sistema escaladas por accesibilidad**, se manifestaba una falla crítica de diseño y usabilidad:
1. **Desbordamiento vertical severo:** El diálogo tenía un tamaño natural excesivo que sobrepasaba la altura disponible de la pantalla, provocando que se centrara y sus extremos fueran expulsados fuera del área visible.
2. **Pérdida de botones de navegación:** Los botones inferiores ("Anterior", "Siguiente", "¡Entendido!") y los puntos indicadores de página desaparecían completamente por debajo del borde de la pantalla. El usuario no podía avanzar ni finalizar el tutorial.
3. **Texto descriptivo truncado:** Las instrucciones con mayor carga textual (como la de *Conducir* con el botón Y o *Interactuar* con el botón X) aparecían cortadas por la mitad sin posibilidad de leer el contenido restante.
4. **Ausencia de gestos táctiles (Swipe):** No existía la posibilidad de cambiar de página deslizando con el dedo a izquierda o derecha, forzando al usuario a buscar botones que en muchos casos ni siquiera eran visibles.

> **Impacto en Accesibilidad y UX:** Este defecto, a pesar de concentrarse en un componente específico, comprometía directamente el primer contacto del usuario con el juego y excluía a personas que utilizan fuentes ampliadas de accesibilidad o juegan en pantallas panorámicas compactas.

### 1.3 Solución Técnica Implementada (El "Después")
Se refactorizó el componente compartido en Kotlin Multiplatform [`ControlsTutorial.kt`](../PolitecnicoOpenWorld/shared/src/commonMain/kotlin/ovh/gabrielhuav/pow/features/settings/ui/ControlsTutorial.kt):
1. **Adopción de `HorizontalPager`:** Se sustituyó el control estático por el componente nativo `HorizontalPager` de Compose Foundation asistido por `rememberPagerState`. Ahora el usuario puede cambiar de página mediante gestos táctiles naturales de deslizamiento (swipe) a la izquierda o derecha con animaciones fluidas y límites de página.
2. **Anclaje seguro de Cabecera y Pie:** La cabecera (Título y botón `"✕"`) y la barra inferior (puntos indicadores y botones de navegación de 44.dp) quedaron anclados dentro de la tarjeta con restricciones de margen (`padding(vertical = 12.dp)` y `widthIn(max = 440.dp)`), garantizando que **nunca salgan de la pantalla**.
3. **Scroll vertical interno por página:** La zona intermedia de contenido se dispuso en un contenedor con `Modifier.weight(1f, fill = false)`, donde cada página implementa `Modifier.verticalScroll(rememberScrollState())`. Si la pantalla es corta o el texto es largo, el contenido se desplaza verticalmente con total fluidez.
4. **Indicadores de página clicables:** Los puntos indicadores ahora detectan toques del usuario y permiten saltar directamente a la página deseada mediante `animateScrollToPage(index)`.
5. **Pruebas unitarias automatizadas KMP:** Se implementó una suite de pruebas en [`ControlsTutorialTest.kt`](../PolitecnicoOpenWorld/shared/src/commonTest/kotlin/ovh/gabrielhuav/pow/features/settings/ui/ControlsTutorialTest.kt) en `commonTest`, verificando la integridad del catálogo de páginas y recursos.

---

## 2. Evidencias Visuales y Multimedia (Antes vs. Después)

Todos los recursos de evidencia se encuentran ubicados en la carpeta [`docs/`](./) del repositorio:

| Estado | Recurso | Descripción de la Evidencia |
|---|---|---|
| **Antes (Falla)** | [`Captura 1.1 - Ventana Tutorial Incompleta.jpg`](Captura%201.1%20-%20Ventana%20Tutorial%20Incompleta.jpg) | Página "Conducir" (Y): El texto se corta a la mitad ("acelera, frena y gira..."), los puntos y los botones inferiores están totalmente fuera de pantalla. |
| **Antes (Falla)** | [`Captura 1.2 - Ventana Tutorial Incompleta.jpg`](Captura%201.2%20-%20Ventana%20Tutorial%20Incompleta.jpg) | Página "Correr" (A): Texto breve donde los puntos apenas tocan el borde inferior y los botones de acción quedan cortados e inaccesibles. |
| **Antes (Falla)** | [`Captura 1.3 - Ventana Tutorial Incompleta.jpg`](Captura%201.3%20-%20Ventana%20Tutorial%20Incompleta.jpg) | Página "Interactuar" (X): La última línea de texto ("objetos de misión") queda rebanada y la barra inferior no se visualiza. |
| **Antes (Falla)** | [`Tutorial Desactualizado.mp4`](Tutorial%20Desactualizado.mp4) | Video demostrativo del comportamiento erróneo original: ausencia de swipe y controles ocultos bajo el marco del teléfono. |
| **Después (Solución)** | [`Captura 2.1 - Ventana Tutorial Corregida.jpg`](Captura%202.1%20-%20Ventana%20Tutorial%20Corregida.jpg) | Tutorial corregido en el Samsung Galaxy A54 5G: Cabecera fija, botones "Anterior" y "Siguiente" 100% visibles y contenido centrado. |
| **Después (Solución)** | [`Captura 2.2 - Ventana Tutorial Corregida.jpg`](Captura%202.2%20-%20Ventana%20Tutorial%20Corregida.jpg) | Demostración de adaptabilidad con fuente grande de accesibilidad y scroll vertical activo sin recorte de controles. |
| **Después (Solución)** | [`Controles Tutorial Corregido.mp4`](Controles%20Tutorial%20Corregido.mp4) | Video completo de la nueva versión: navegación por gestos swipe (deslizamiento horizontal), scroll vertical en textos largos y salto directo mediante puntos indicadores. |

---

## 3. Matriz de Pruebas de Calidad (QA)

El plan detallado con los 6 casos de prueba obligatorios, análisis de riesgos y resultados se encuentra en:  
👉 **[Documento Completo de Pruebas: docs/pruebas.md](pruebas.md)**

### Resumen de Cobertura de Pruebas

| ID Caso | Categoría de Prueba | Criterio / Riesgo | Dispositivo Probado | Resultado |
|---|---|---|---|:---:|
| **CP-01** | **Ruta Feliz** | CA-01, R-02 | Samsung Galaxy A54 5G / AVD | **APROBADO** |
| **CP-02** | **Condición Límite** | CA-02, R-01 | Samsung Galaxy A54 5G (Landscape ~380dp) | **APROBADO** |
| **CP-03** | **Regresión** | R-03 | Samsung Galaxy A54 5G (Tutorial Interiores) | **APROBADO** |
| **CP-04** | **Navegación y Ciclo de Vida** | R-02 | Samsung Galaxy A54 5G (Atrás, ✕, Home) | **APROBADO** |
| **CP-05** | **Accesibilidad** | CA-02, R-01 | Samsung Galaxy A54 5G (Fuente al 140%) | **APROBADO** |
| **CP-06** | **Compatibilidad / Entorno** | CA-01 | Samsung Galaxy A54 5G (Modo Oscuro / Indicadores) | **APROBADO** |

---

## 4. Verificaciones Automáticas e Integración Continua (CI Gates)

Todas las verificaciones del workflow oficial del proyecto ([`.github/workflows/pr-quality-gate.yml`](../.github/workflows/pr-quality-gate.yml)) fueron ejecutadas localmente en el entorno de desarrollo:

1. **Guarda de Nombres KMP (`bash tools/check_kmp_test_names.sh`):**
   - Resultado: `✅ Nombres de test compatibles con Kotlin/Native en 'shared/src/commonTest'.`
2. **Pruebas Unitarias de `:shared` (`gradle :shared:testAndroidHostTest`):**
   - Resultado: `BUILD SUCCESSFUL` (0 fallos). Incluye las nuevas pruebas en `ControlsTutorialTest.kt`.
3. **Pruebas Unitarias de `:app` (`gradle :app:testDebugUnitTest`):**
   - Resultado: `BUILD SUCCESSFUL` (0 fallos).
4. **Construcción Debug Completa (`gradle :app:assembleDebug`):**
   - Resultado: `BUILD SUCCESSFUL`. Generación exitosa de `app-debug.apk`.
5. **Compilación para Simulador iOS (`gradle :shared:compileKotlinIosSimulatorArm64`):**
   - Resultado: `BUILD SUCCESSFUL`. Validación de compatibilidad multiplataforma de `HorizontalPager` y Compose Multiplatform.
6. **Análisis Estático de Código (`detekt-cli.bat`):**
   - Resultado: `Exit 0`. Cero violaciones o code smells introducidos.

---

## 5. Declaración sobre el Uso de Herramientas de Inteligencia Artificial

En cumplimiento con los lineamientos académicos del curso, se declara de forma transparente el uso de herramientas de Inteligencia Artificial durante la realización de este examen:
- **Herramienta utilizada:** Asistente de IA (Gemini 3.8 Flash / Antigravity).
- **Propósito y alcance del uso:**
  1. Asistencia como compañero de programación en pares (*pair programming*) para la lectura, inspección y navegación del código fuente del proyecto POW.
  2. Apoyo en la formulación de la solución técnica con Compose Multiplatform (`HorizontalPager`, gestión de restricciones y scroll anidado).
  3. Ejecución y diagnóstico de tareas de construcción de Gradle y análisis estático con Detekt.
  4. Estructuración y redacción de la matriz de casos de prueba de QA y del reporte técnico en Markdown.
- **Responsabilidad y autoría:** El diseño de la solución, la selección de la problemática a corregir, la ejecución de las pruebas en el dispositivo físico Samsung Galaxy A54 5G, la captura de evidencias en video e imagen, y el control de commits en GitHub Desktop fueron dirigidos y validados personalmente por el alumno Aragón Martínez Manuel Alejandro.

---

## 6. Dictamen de Calidad y Conclusiones

* **Conclusión Técnica:** La refactorización elimina de raíz el problema de recorte vertical y falta de botones en el tutorial de controles, enriqueciendo además la interacción con gestos táctiles horizontales e indicadores interactivos. La solución es 100% multiplataforma al residir en `:shared` y mantiene intactos todos los contratos de arquitectura, MVVM y directrices del proyecto.
* **Dictamen:** **APROBADO PARA MERGE (GO)**. El cambio cumple satisfactoriamente todos los criterios de aceptación, cuenta con red de pruebas automatizadas y manuales reproducibles, y supera los Quality Gates del proyecto.
