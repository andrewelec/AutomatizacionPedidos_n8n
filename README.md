# AutomatizacionPedidos_n8n
Automatización con IA para recepción de pedidos en pequeños negocios
# Automatización de toma de pedidos e inventario con n8n, Telegram e IA

## El problema

En muchos negocios pequeños y medianos —tiendas de repuestos, distribuidoras, ferreterías, minimercados— la toma de pedidos sigue siendo principalmente de forma manual. Un empleado o peor aún, el dueño del negocio recibe los pedidos por WhatsApp, Telegram, llamada o correo, y para cada uno tiene que: leer el mensaje, entender qué producto quiere el cliente (que casi nunca usa el nombre exacto del catálogo), revisar el inventario en una hoja de cálculo, confirmar si hay stock, proponer alternativas cuando no hay, escribir el pedido en otra hoja y, por último, descontar el inventario y vigilar cuándo reponer.

Ese empleado o dueño normalmente no hace solo eso: también atiende clientes, gestiona proveedores, responde correos y resuelve imprevistos. La toma de pedidos compite con el resto de sus tareas, se vuelve un cuello de botella, y los errores aparecen: pedidos mal anotados, productos vendidos sin stock real, inventario desactualizado, reposiciones tardías de inventario, demoras en la toma de pedido y pérdida de potenciales clientes.

Es un proceso que sigue reglas claras (interpretar, cruzar con inventario, decidir, registrar, actualizar) y que se repite muchas veces al día. Justamente por eso es un candidato ideal para automatizar: alta frecuencia, lógica definida y bajo valor agregado en la ejecución manual.

## Mapeo del flujo (antes de automatizar)

**Pasos en orden**
1. Llega un pedido del cliente por mensaje.
2. Se interpreta qué productos y cantidades quiere.
3. Se cruza cada producto con el inventario.
4. Se decide: ¿hay stock? ¿el producto es ambiguo? ¿no existe? ¿está agotado?
5. Se registra el pedido o se negocia una alternativa con el cliente.
6. Se descuenta el inventario y se vigila el stock mínimo.

**Entradas**
- Mensaje del cliente en lenguaje natural.
- Catálogo e inventario (Google Sheets): ID del producto, nombre, stock, precio, stock mínimo, sustitutos.

**Salidas**
- Pedido registrado con fecha, cliente, ID del producto, cantidad, precio y subtotal.
- Confirmación o propuesta de alternativa enviada al cliente.
- Inventario actualizado y alertas de reposición.

**Responsable**
- Antes: el empleado o dueño, en cada paso.
- Después: el flujo de n8n de forma autónoma; el empleado o dueño solo supervisa excepciones y casos muy especiales.

**Puntos de falla (que la automatización mitiga)**
- Interpretación errónea del pedido: se resuelve con IA mediante un análisis e interpretación del lenguaje natural del usuario.
- Venta sin stock real: se resuelve con verificación automática antes de confirmar.

## El flujo construido con n8n

El flujo recibe los pedidos por Telegram, los interpreta con IA (Gemini) y ejecuta un árbol de decisión conectado a un inventario en Google Sheets, el cual en este caso se ejemplifica con una tienda de repuestos y accesorios electrónicos:

1. **Recepción.** Un bot de Telegram recibe el mensaje del cliente.
2. **Interpretación con IA.** Gemini lee el texto en lenguaje natural ("necesito 3 RAM de 16GB") y lo mapea contra el catálogo, devolviendo ID del producto, cantidad y nivel de confianza para cada producto.
3. **Cruce con inventario.** El flujo lee el inventario en Google Sheets y verifica el stock de cada ID de producto.
4. **Árbol de decisión.** Cada producto se clasifica en una de cuatro rutas:
   - **Disponible:** se crea el pedido y se confirma al cliente.
   - **Sin stock:** se proponen sustitutos con existencias mediante botones interactivos; el cliente elige y se crea el pedido del sustituto.
   - **Ambiguo:** cuando el pedido encaja con varios productos (ej. "RAM 16GB": DDR4 o DDR5), se ofrecen las opciones por botones y el cliente decide.
   - **No existe:** se avisa que el producto no está en catálogo.
5. **Gestión de la conversación.** Las respuestas del cliente (clics en botones) se capturan y reanudan el flujo para cerrar el pedido elegido.
6. **Actualización de inventario.** Tras cada pedido confirmado, el stock se descuenta automáticamente y, si un producto cae por debajo de su mínimo, se envía una alerta de reposición.

Lo que hace único a este flujo es que no es un simple registro de pedidos: interpreta lenguaje natural, toma decisiones y negocia, todo de forma autónoma.

## Una rutina normal del empleado o dueño (sin la herramienta)

Para dimensionar el ahorro, veamos lo que toma procesar pedidos a mano. Tiempos estimados por pedido:

| Tarea manual | Tiempo aproximado |
|---|---|
| Leer e interpretar el mensaje del cliente | 1–2 min |
| Buscar cada producto en el inventario | 2–3 min |
| Verificar stock y decidir | 1 min |
| Si falta stock: buscar alternativa y escribir al cliente | 3–5 min |
| Esperar respuesta y cerrar el pedido | variable |
| Anotar el pedido en la hoja | 2 min |
| Descontar inventario manualmente | 1–2 min |
| **Total por pedido** | **10–15 min** (más si hay negociación) |

En un día con, por ejemplo, 20 pedidos, eso son entre 3 y 5 horas dedicadas solo a tomar pedidos, fragmentando la atención del empleado o dueño y compitiendo con sus demás tareas. Además, cada interrupción tiene un costo oculto: retomar la concentración, revisar que no haya errores, corregir inventario desfasado.

## Cuánto ayuda el flujo (ahorro de tiempo y dinero)

Con el flujo automatizado, el tiempo de operación del empleado o dueño por pedido baja de 10–15 minutos a prácticamente cero en los casos estándar, ya que el sistema interpreta, decide, registra y actualiza solo. El empleado o dueño únicamente interviene en excepciones poco frecuentes.

**Ahorro de tiempo estimado**

| Escenario | Manual | Automatizado | Ahorro |
|---|---|---|---|
| 1 pedido | 12 min | 0 min operativos | 12 min |
| 20 pedidos/día | 4 horas | minutos de supervisión | 3.5–4 h/día |
| Mes (22 días laborales) | 88 horas | supervisión mínima | **80 horas/mes** |

**Ahorro de dinero (ilustrativo)**

Si el costo cargado de una hora del empleado o dueño es, por ejemplo, de 6 USD, recuperar 80 horas al mes equivale a 480 USD mensuales liberados, que el empleado o dueño puede dedicar a tareas de mayor valor (atención al cliente, proveedores, crecimiento). A esto se suman ahorros menos visibles pero reales:

- Menos ventas perdidas por no detectar a tiempo el stock agotado.
- Menos sobrecompra o quiebres de stock gracias a las alertas de reposición.
- Menos errores de transcripción y de inventario desfasado.
- Atención más rápida al cliente, que recibe respuesta inmediata a cualquier hora, no solo cuando el empleado o dueño está disponible.

## Resultado concreto

El flujo convierte un proceso manual, lento y propenso a errores, que consumía varias horas diarias de un empleado o dueño saturado, en un sistema autónomo que recibe el pedido, lo interpreta, decide, negocia con el cliente y lo registra. El empleado o dueño pasa de ejecutar cada pedido a supervisar excepciones, liberando del orden de 80 horas al mes para tareas de mayor valor.
