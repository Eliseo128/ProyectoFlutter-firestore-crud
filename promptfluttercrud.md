Aquí tienes un **prompt optimizado y listo para copiar/pegar** que puedes usar con cualquier IA (incluyéndome). Está estructurado para garantizar una respuesta técnica, ordenada, sin saltos lógicos y con código funcional actualizado.

```text
Actúa como un desarrollador senior experto en Flutter, Dart y Firebase Firestore. Necesito que me guíes paso a paso, en orden lógico y sin omitir detalles, para crear una aplicación Flutter con un formulario de 3 campos (nombre, edad, puesto) conectada a Firestore, implementando un CRUD completo (Crear, Leer, Actualizar, Eliminar).

Sigue EXACTAMENTE esta estructura:

1. 🛠️ Creación del proyecto: Comandos para generar un nuevo proyecto Flutter.
2. 📦 Dependencias: Qué paquetes agregar a `pubspec.yaml` (`firebase_core`, `cloud_firestore`, etc.) y comando para instalarlos. Especifica versiones compatibles con Flutter 3.x y Firebase SDK 10+ (2025-2026).
3. 🔗 Configuración de Firebase: Pasos concisos para registrar la app en Firebase Console, descargar `google-services.json` / `GoogleService-Info.plist`, y inicializar Firebase en `main.dart`.
4. 📐 Modelo de datos: Clase Dart (`Empleado`) con campos: `id` (String), `nombre`, `edad` (int), `puesto`. Incluir `toJson()`, `fromJson()`, constructor y `copyWith()` si aplica.
5. 🗄️ Servicio Firestore: Clase/repository (`FirestoreService`) con métodos asíncronos: `crear()`, `leerTodos()`, `actualizar()`, `eliminar()`. Incluir manejo de errores básico y retornar `Stream` para lectura en tiempo real.
6. 🖼️ Interfaz de usuario (UI):
   - Formulario con `TextFormField` para nombre, edad (solo numérico) y puesto.
   - Validación básica: campos no vacíos, edad > 0.
   - Lista usando `StreamBuilder` que muestre registros con botones de Editar y Eliminar.
   - Estados de carga y error visibles en la UI.
7. 🔌 Integración CRUD: Conectar UI con `FirestoreService` usando `setState` (o `Provider` si es más limpio; justifica tu elección). Manejar estado de edición vs creación.
8. 📁 Estructura de archivos: Muestra la estructura de carpetas recomendada para este proyecto.
9. ▶️ Ejecución y pruebas: Comandos para correr la app, y 3 errores comunes + soluciones rápidas.
10. 💻 Código completo: Proporciona TODO el código listo para copiar, dividido por archivo, con comentarios explicativos en español y siguiendo buenas prácticas de Flutter (separación de lógica/UI, tipos estrictos, null-safety).

Requisitos adicionales:
- Usa `StreamBuilder` para la lista y `FutureBuilder` donde corresponda.
- Maneja correctamente los `try/catch` y muestra `SnackBar` o diálogos en errores.
- La edad debe guardarse como número en Firestore.
- Explica brevemente cada paso ANTES de mostrar el código.
- Mantén el código modular, legible y actualizado a 2025-2026.

Responde en español y asegúrate de que cada paso sea ejecutable sin saltos lógicos. Si algún paso requiere configuración manual (como Firebase Console), indícalo claramente con viñetas numeradas.
```

### 💡 Consejos para usarlo:
1. **Cópialo tal cual** y pégalo en tu chat con IA.
2. Si la respuesta se corta por límite de tokens, escribe: `"Continúa exactamente desde donde quedaste, manteniendo el formato y numeración"`.
3. Te recomiendo crear una carpeta `lib/src/` con subcarpetas: `models/`, `services/`, `screens/` y `widgets/` para mantener el orden que generará el prompt.

¿Quieres que **ejecute este prompt ahora mismo** y te entregue la guía completa paso a paso con todo el código listo para tu proyecto? Solo dime `"Sí, generalo"` y lo hago inmediatamente.
