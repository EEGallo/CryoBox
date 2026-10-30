# CryoBox Web — Diseño (spec)

Fecha: 2026-10-30 · Muestra final: **2026-12-05** · Metodología: SDD (Spec-Driven Development)

Leyenda: **[Decidido]** lo dijo el equipo · **[Mi decisión]** la tomé yo, el equipo puede cambiarla · **[Pendiente]** falta un dato del equipo.

## 1. Intención

CryoBox es una caja refrigerante para trasladar medicamentos y vacunas a largas distancias. Nace de una problemática real y es un desafío de aprendizaje de robótica, ciencia de datos y biotecnología. Es un proyecto del programa **Conectados Mendoza Futura**, **Nodo Reynaldo Merín**, hecho por 5 integrantes. **[Decidido]**

La web se presenta en la **muestra final de año** (5 de diciembre de 2026), junto a los demás proyectos. **[Decidido]**

**Objetivo principal:** que quien la visite se lleve la idea central de lo que hace la CryoBox, en pocos segundos. **[Decidido]**

## 2. Objetivos, no-objetivos y anti-objetivos

**Objetivos [Decidido]**
- Visor 3D interactivo de la caja y su sistema de enfriamiento, con etiquetas que referencian cada parte.
- Explicación breve de qué es la CryoBox.
- Sección del proceso de desarrollo.
- Sección de integrantes con sus roles.

**No-objetivos [Mi decisión]**
- Sin backend, base de datos, cuentas de usuario ni formularios.
- Sin versión optimizada para celular (se muestra en computadora). Que no se rompa en pantallas chicas, pero no es prioridad.
- Sin datos en vivo del prototipo (todavía no hay mediciones).

**Anti-objetivos [Mi decisión, basada en la conversación]**
- La web no debe afirmar que la CryoBox ya enfría a una temperatura determinada. Hoy es un **prototipo en desarrollo, sin pruebas ni mediciones**. Todo texto debe decir "diseño previsto" o "prototipo".
- No copiar el diagrama de referencia que pasó el equipo: tiene errores (rótulo "HOT EFRIGRATOR", marca "cold refrigerant" en el tramo caliente) y muestra un compresor que el sistema no tiene. Se redibuja.
- No mostrar el modelo como un producto terminado ni como apto para uso médico.

## 3. Restricciones

- Fecha límite: 5 de diciembre de 2026 (36 días desde hoy). **[Decidido]**
- Nadie del equipo programa web: el contenido (textos, nombres, etapas) debe poder editarse en **un archivo simple**, sin tocar el código. **[Decidido]**
- Se muestra en una computadora con pantalla grande, con mouse y teclado. Textos y etiquetas grandes, legibles a distancia. **[Decidido]**
- Debe funcionar **sin internet** durante la muestra (todo local, sin CDN ni recursos externos). **[Mi decisión]**
- Idioma: español. **[Mi decisión]**
- Seguridad: el gas es isobutano, que es inflamable. La web incluye una nota visible de seguridad. **[Mi decisión]**

## 4. Tecnología **[Mi decisión, el equipo la aceptó por defecto]**

- Sitio estático con **Vite + Three.js**. Sin servidor.
- Publicación gratuita en GitHub Pages o Netlify, más una carpeta lista para abrir localmente en la muestra.
- Contenido editable en un único archivo (`content.js` o JSON), con la lista de integrantes, etapas, textos y rótulos.

## 5. Diseño visual **[Decidido: estilo C]**

- Degradé azul frío (de azul profundo a azul hielo), con sensación de frío, amigable, sin estética de ciencia ficción.
- Portada: etiqueta "CRYOBOX · PROTOTIPO", frase principal, visor 3D y botón "Explorar".
- Tipografía grande y contrastada. Los valores exactos de color y tipografía son del builder.
- Espacios reservados para los logos de Conectados Mendoza Futura, Nodo Reynaldo Merín y CryoBox (todavía no existen). **[Decidido]**

## 6. Estructura: una sola página con scroll **[Decidido]**

Menú fijo arriba que salta a cada sección. El orden es **[Mi decisión]**:

1. **Portada.** Frase principal + visor 3D interactivo con etiquetas.
2. **¿Qué es CryoBox?** Explicación breve: el problema (trasladar medicamentos refrigerados a largas distancias) y la solución.
3. **Cómo funciona.** Circuito paso a paso, con un diagrama redibujado (ver §8).
4. **Proceso de desarrollo.** Línea de tiempo. El equipo pasará un documento completo después; hasta entonces, **etapas provisorias** marcadas como "a completar". **[Decidido]**
5. **Equipo.** Los 5 integrantes con sus roles. Roles = "pendiente de definir". **[Decidido]** Sin fotos por ahora.
6. **Pie.** Espacios para logos, nota de seguridad y crédito del programa.

**Frase principal [Mi decisión, editable]:** "CryoBox: vacunas frías, sin importar la distancia." La frase del equipo ("El sistema innovador de refrigeración de vacunas") queda como alternativa. **[Pendiente]** que el equipo elija.

## 7. Visor 3D

- El modelo **no existe** y se crea desde cero. **[Decidido]** Lo construyo por código con Three.js a partir de formas simples (cajas, tubos, hélices), sin modelado externo. **[Mi decisión]**
- Interacción con mouse: arrastrar para rotar, rueda para zoom, rotación automática suave cuando no hay actividad. **[Mi decisión]**
- Etiquetas grandes sobre cada pieza. Al pasar el mouse o hacer clic sobre una pieza, aparece una descripción de una o dos líneas. **[Mi decisión]**
- Piezas a representar y etiquetar **[Decidido, según lo contado por el equipo]**:
  - Conservadora de telgopor (la caja)
  - Evaporador (serpentina de cobre espiralada)
  - Condensador (serpentina de cobre espiralada)
  - Capilar (tubo de cobre con 3 vueltas, soldado con estaño)
  - Estructura de madera que sostiene las serpentinas
  - Cooler (ventilador)
  - Bomba de 12 V
  - Tubos de PVC (bomba–condensador y bomba–evaporador)
  - Batería
  - Gas isobutano (indicado en el circuito)
- La ubicación de cada pieza respecto de la caja (qué va dentro y qué fuera) es **[Pendiente]** (ver §10). Mientras tanto, supongo: evaporador dentro de la caja, condensador fuera. **[Mi decisión, a confirmar]**

## 8. "Cómo funciona"

- Diagrama redibujado de **un circuito sin compresor**, tal como lo describió el equipo: el gas circula entre ambas serpentinas con la bomba. **[Decidido]**
- Pasos: el gas pasa por el capilar, se enfría y pasa por el evaporador, donde absorbe calor del interior; luego va al condensador, donde libera calor, ayudado por el cooler; la bomba lo hace volver a circular.
- **Advertencia técnica [Mi decisión]:** una bomba mueve líquido, no comprime vapor, y condensar el isobutano a temperatura ambiente requiere presión. Sin pruebas, no sabemos si el circuito enfría lo esperado. Por eso el texto describe el **principio de diseño previsto** y deja claro que se está validando. Si las pruebas cambian el diseño, se actualiza el texto.

## 9. SDD: cómo se trabaja **[Mi decisión]**

1. **Spec** (este documento) → aprobada por el equipo.
2. **Plan** de implementación con tareas pequeñas, cada una con criterio de aceptación verificable.
3. **Implementación** tarea por tarea, comprobando cada criterio.
4. **Verificación** final contra esta spec antes de la muestra, ensayada en la computadora real de la muestra.

Hitos tentativos (ajustables en el plan): estructura y contenido base → modelo 3D con etiquetas → diagrama y línea de tiempo → pulido y prueba offline → ensayo final antes del 5/12.

## 9b. Decisiones adicionales tras la revisión **[Mi decisión]**

- **Nivel de realismo del 3D:** modelo **esquemático y estilizado** (colores planos, formas simples; cobre y madera sugeridos con color, sin texturas fotorrealistas). El trabajo del 3D tiene un tope de tiempo en el plan para no consumir todo el calendario.
- **Disposición de piezas:** las posiciones viven en configuración (no hardcodeadas), para corregirlas barato cuando el equipo confirme el montaje. La caja se muestra **semitransparente / con corte** para ver lo que va adentro, y con flechas que indican el sentido de circulación del gas.
- **Qué va en el archivo de contenido:** nombres de piezas, descripciones, pasos de "Cómo funciona", etapas, integrantes y textos. La geometría y las posiciones quedan en el código. El archivo se edita antes de la muestra y se regenera la web (lo hago yo o con un script de un solo comando que documento).
- **Diagrama de "Cómo funciona":** SVG redibujado con flechas animadas del circuito; al elegir un paso se resalta la pieza en el 3D (esto último es opcional y va al final si hay tiempo).
- **Plan:** se planifica con los valores "Mi decisión" como definitivos y los pendientes como marcadores en el archivo de contenido. Hay un punto de replanificación si el equipo cambia la lista de piezas o la estructura.
- **Voltajes:** se muestra "bomba 12 V" y **no** se muestra el voltaje de la batería hasta que el equipo lo confirme.

## 9c. Respuestas del equipo y entrega offline

- **Montaje [Decidido: se usa mi suposición]:** evaporador dentro de la conservadora, condensador fuera. El capilar une el extremo del condensador con el del evaporador. Se corrige si el equipo detecta un error.
- **Computadora de la muestra [Decidido]:** Windows con Chrome.
- **Entrega offline [Mi decisión]:** además del sitio publicado, se entrega un **único `index.html` autocontenido** (todo el código y los recursos incluidos), que abre con doble clic en Chrome sin internet ni servidor. Se prueba en Windows + Chrome antes del 5/12. Si el 3D va lento en esa máquina, hay un modo de calidad reducida.

## 10. Pendientes del equipo

- Roles de cada integrante.
- Documento completo del proceso de desarrollo (reemplaza las etapas provisorias).
- Logos de Conectados Mendoza Futura, Nodo Reynaldo Merín y CryoBox.
- Frase principal definitiva.
- Voltaje de la batería: dijeron 9 V para el cooler y la bomba, pero la bomba es de 12 V.
- Qué extremo del capilar va a cada serpentina (escribieron "condensador" dos veces) y ubicación de cada pieza dentro o fuera de la caja.
- Fotos del equipo y del prototipo, si las quieren incluir.
- Resolución y placa de video de la computadora de la muestra (opcional; ayuda a ajustar la calidad del 3D).

## 11. Criterios de éxito

- Un visitante entiende en 30 segundos qué es y qué hace CryoBox.
- El visor 3D se maneja con el mouse sin instrucciones, y todas las piezas listadas tienen etiqueta.
- Cambiar un nombre, rol o etapa se hace editando un solo archivo.
- La web funciona completa sin conexión a internet.
- Ninguna afirmación sobre rendimiento que no esté respaldada por pruebas.
