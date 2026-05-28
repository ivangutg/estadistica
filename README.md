# 🌮 Sistema de Gestión "Los Consentidos"

Aplicación web *Serverless* (JAMstack) diseñada para la gestión integral de un restaurante. Permite la administración de órdenes, control de inventario, gestión de personal, mapa de mesas y recepción de reseñas mediante códigos QR. 

El proyecto está construido con **HTML, CSS y JavaScript puro (Vanilla JS)** en el frontend, conectado directamente a una base de datos **PostgreSQL en la nube mediante Supabase**.

---

## 💻 Módulos de la Interfaz (Frontend)

El sistema está dividido en secciones interactivas que se actualizan de forma asíncrona mediante llamadas a la API REST de Supabase:

* **🛒 Órdenes (Punto de Venta):** Permite abrir nuevas órdenes asignando a un empleado responsable. Cuenta con un carrito de compras interactivo que calcula subtotales y el total de la orden en tiempo real.
* **🍽️ Menú:** Visualización del catálogo de alimentos filtrable por categorías. Permite registrar nuevos platillos indicando nombre, precio y categoría.
* **🧺 Inventario:** Control estricto de insumos. Calcula las existencias reales sumando las entradas y muestra alertas de color (Agotado, Bajo, OK) basadas en un stock mínimo definido.
* **👨‍💼 Personal:** Gestión de la plantilla de empleados. Permite registrar altas de meseros con información de contacto, asignación de rol y turno (Mañana, Tarde, Noche).
* **🏷️ Mesas:** Plano virtual con 12 mesas. Muestra visualmente el estado (Libre / Ocupada) y un cronómetro en tiempo real con los minutos transcurridos desde que la mesa fue ocupada.
* **💳 Caja:** Módulo de liquidación de cuentas pendientes. Permite dividir la cuenta entre varios comensales, seleccionar el método de pago (Efectivo, Tarjeta, Transferencia) y generar un ticket de compra con formato de impresión.
* **⭐ Reseñas y Código QR:** Generador dinámico de códigos QR únicos por mesa (consumiendo la API de `qrserver`). Los clientes pueden escanearlo para acceder a una vista pública y dejar una calificación de 1 a 5 estrellas junto con comentarios.
* **🔔 Notificaciones Inteligentes:** Un servicio en segundo plano revisa automáticamente (cada 60 segundos) el sistema para alertar sobre ingredientes con stock bajo y órdenes que no han sido cobradas.

---

## 🗄️ Arquitectura de la Base de Datos (Supabase / PostgreSQL)

El sistema utiliza un esquema relacional diseñado para mantener la integridad de los datos de las operaciones diarias. 

### Catálogos Principales
* `roles`: Define los permisos básicos (Mesero, Cocinero, Gerente, Cajero).
* `empleado`: Almacena la información del personal, sus fechas de ingreso/egreso, su rol y turno asignado.
* `categorias`: Clasificación de los platillos (Entradas, Postres, etc.).
* `platillo`: Almacena el nombre, precio y categoría de los productos ofrecidos.
* `ingredientes`: Catálogo base de insumos, incluyendo su unidad de medida (KG, PZ, LTS) y el nivel mínimo para disparar alertas de stock.

### Tablas Transaccionales
* `orden`: Cabecera de los pedidos; registra la fecha y el empleado que atiende.
* `detalle_orden`: Relaciona cada `orden` con múltiples registros de `platillo`, indicando las cantidades solicitadas.
* `inventario`: Funciona como una bitácora de entradas. Cada vez que se agrega stock, se crea un registro aquí; la existencia total se calcula sumando estas entradas.
* `ingreso`: Registra los cobros de las órdenes. Guarda el monto, la fecha, el método de pago y entre cuántos comensales se dividió la cuenta.
* `mesa`: Controla el piso del restaurante. Mantiene el estado booleano de disponibilidad y la marca de tiempo de ocupación.
* `resena`: Almacena el feedback de los clientes, vinculando la mesa, calificación numérica, comentario y fecha.

### Vistas y Seguridad
* `v_stock`: Vista SQL que agrupa la tabla `ingredientes` con `inventario` para entregar en tiempo real las existencias consolidadas y el estado textual del stock ("OK", "BAJO", "AGOTADO").
* **Seguridad a Nivel de Fila (RLS):** Todas las tablas tienen habilitado *Row Level Security* con políticas de acceso para permitir la lectura y escritura segura desde el frontend estático.

---

## 🚀 Tecnologías Utilizadas
* **Frontend:** HTML5, CSS3, JavaScript (ES6+)
* **Backend / BaaS:** Supabase (PostgreSQL, APIs REST automáticas)
* **Hosting:** GitHub Pages
* **APIs Externas:** QR Server API
