¡Hola! Claro que sí, será un placer guiarte con este proyecto de inicio a fin. A continuación, te presento el desarrollo paso a paso siguiendo la estructura solicitada, aplicando las mejores prácticas actuales de Flutter y Firebase.

### 1. 🛠️ Creación del proyecto

Para empezar, necesitamos generar la estructura base de nuestra aplicación Flutter. Abre tu terminal y ejecuta los siguientes comandos:

```bash
flutter create formulario_firestore
cd formulario_firestore
```
> **Nota**: Este comando creará un proyecto vacío compatible con las plataformas actuales (Android, iOS, Web, etc.).

### 2. 📦 Dependencias

Necesitamos agregar los paquetes oficiales para conectar con Firebase y Firestore. En la terminal (dentro de la carpeta del proyecto), ejecuta:

```bash
flutter pub add firebase_core cloud_firestore
```

Si revisas tu archivo `pubspec.yaml`, verás agregadas estas dependencias. Para 2025/2026, las versiones estables rondarán alrededor de:
```yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^3.0.0 # o versión superior
  cloud_firestore: ^5.0.0 # o versión superior
```

### 3. 🔗 Configuración de Firebase

Aunque existe el CLI moderno (`flutterfire`), como solicitaste la vía tradicional, estos son los pasos para la consola:

1. Ve a [Firebase Console](https://console.firebase.google.com/) y crea un nuevo proyecto.
2. **Para Android**: 
   - Añade una app Android con el package name de tu app (se encuentra en `android/app/build.gradle` como `applicationId`).
   - Descarga el archivo `google-services.json` y colócalo en la carpeta `android/app/`.
   - *Nota: Asegúrate de habilitar Cloud Firestore en la sección "Compilación > Firestore Database" desde la consola de Firebase.*
3. **Para iOS**: 
   - Añade una app iOS con el Bundle ID de tu app.
   - Descarga `GoogleService-Info.plist` y colócalo en la raíz de `ios/Runner/` usando Xcode (arrástralo hacia el proyecto).
4. Inicializa Firebase en tu `main.dart` usando `await Firebase.initializeApp();` asegurándote de usar `WidgetsFlutterBinding.ensureInitialized();` antes.

### 4. 📐 Modelo de datos

Crearemos una clase `Empleado` que representará a nuestra entidad en Firestore. Esto nos asegura un tipado estricto al momento de subir o descargar datos, evitando errores de tipeo en los nombres de los campos.

### 5. 🗄️ Servicio Firestore

Para no mezclar la lógica de la base de datos con la Interfaz de Usuario, creamos un repositorio (`FirestoreService`). Este se encargará exclusivamente de las operaciones a la base de datos, retornando un `Stream` para la lectura en tiempo real y manejando errores con un `try-catch` que lanza excepciones que la UI debe atrapar y mostrar.

### 6. 🖼️ Interfaz de usuario (UI)

Nuestra UI tendrá dos partes principales en la misma pantalla:
1. **Formulario (Arriba)**: Con 3 `TextFormField`. Usaremos validadores (`validator`) nativos de Flutter para asegurar que no envíen datos en blanco, y que la edad sea un número mayor a cero usando `int.tryParse`.
2. **Listado (Abajo)**: Un `StreamBuilder` escuchando cambios de Firestore, mostrando un indicador de progreso (`CircularProgressIndicator`) mientras carga y una lista nativa una vez reciba los datos. Cada ítem tendrá iconos (lapiz y basurero).

### 7. 🔌 Integración CRUD

**Elegimos `setState` directamente en un `StatefulWidget`** en este ejemplo.
*Justificación*: Para un CRUD de una sola pantalla, agregar el boilerplate de `Provider` o `Riverpod` resulta excesivo y resta legibilidad a la demostración de Firestore. Usaremos variables en el estado para los controladores de texto y para rastrear si estamos "creando" o "actualizando" un registro (verificando si el `id` actual es nulo). Si el proyecto llegase a escalar a más pantallas, separar la lógica del formulario hacia una gerencia de estado sería el siguiente paso.

### 8. 📁 Estructura de archivos

Se recomienda mantener el código modular separando responsabilidades:

```text
lib/
 📦 main.dart                  # Punto de entrada e inicialización
 📦 models/
 ┃  ┗ 📜 empleado.dart         # Modelo de datos
 📦 services/
 ┃  ┗ 📜 firestore_service.dart# Lógica de base de datos
 📦 screens/
    ┗ 📜 home_screen.dart      # Pantalla principal con UI y Formulario
```

### 9. ▶️ Ejecución y pruebas

Para correr el proyecto:
```bash
flutter run
```

**Errores comunes y soluciones:**
1. ❌ **Execution failed for task ':app:processDebugGoogleServices'.**
   *Causa:* Falta el archivo, o el plugin no está registrado en los `build.gradle`. 
   *Solución:* Sigue bien la documentación para las dependencias granulares de Google Services en Android.
2. ❌ **Cloud Firestore Plugin - PERMISSION_DENIED.**
   *Causa:* Las reglas de seguridad de Firestore bloquean las lecturas/escrituras.
   *Solución:* En modo de prueba temporal, ve a Firestore -> Reglas y pon `allow read, write: if true;`
3. ❌ **Unsupported operation: int depends on an invalid format.**
   *Causa:* El campo enviado de edad estaba en `String`.
   *Solución:* Asegurarnos de hacer `.toInt()` o `int.parse()` antes de mandarlo en el `.toJson()`.

---

### 10. 💻 Código completo

Aquí tienes todo listo para copiar y pegar según la estructura del **Paso 8**. 

#### `lib/models/empleado.dart`
```dart
class Empleado {
  final String id;
  final String nombre;
  final int edad;
  final String puesto;

  Empleado({
    required this.id,
    required this.nombre,
    required this.edad,
    required this.puesto,
  });

  // Convertidor desde Mapa (de Firestore hacia nuestra App)
  factory Empleado.fromJson(Map<String, dynamic> json, String id) {
    return Empleado(
      id: id,
      nombre: json['nombre'] as String? ?? '',
      edad: json['edad'] as int? ?? 0,
      puesto: json['puesto'] as String? ?? '',
    );
  }

  // Convertidor hacia Mapa (de nuestra App hacia Firestore)
  Map<String, dynamic> toJson() {
    return {
      'nombre': nombre,
      'edad': edad,
      'puesto': puesto,
    };
  }

  // Permite copiar el objeto alterando solo algunos campos.
  Empleado copyWith({
    String? id,
    String? nombre,
    int? edad,
    String? puesto,
  }) {
    return Empleado(
      id: id ?? this.id,
      nombre: nombre ?? this.nombre,
      edad: edad ?? this.edad,
      puesto: puesto ?? this.puesto,
    );
  }
}
```

#### `lib/services/firestore_service.dart`
```dart
import 'package:cloud_firestore/cloud_firestore.dart';
import '../models/empleado.dart';

class FirestoreService {
  // Referencia a la colección 'empleados' en la BD
  final CollectionReference _db = FirebaseFirestore.instance.collection('empleados');

  // CREAR (Create)
  Future<void> crear(Empleado empleado) async {
    try {
      await _db.add(empleado.toJson());
    } catch (e) {
      throw Exception('Error al crear empleado: $e');
    }
  }

  // LEER (Read) - Usamos un Stream para estar en tiempo real
  Stream<List<Empleado>> leerTodos() {
    return _db.snapshots().map((snapshot) {
      return snapshot.docs.map((doc) {
        // Convertimos cada Documento devuelto a un objeto Empleado
        return Empleado.fromJson(doc.data() as Map<String, dynamic>, doc.id);
      }).toList();
    });
  }

  // ACTUALIZAR (Update)
  Future<void> actualizar(Empleado empleado) async {
    try {
      await _db.doc(empleado.id).update(empleado.toJson());
    } catch (e) {
      throw Exception('Error al actualizar empleado: $e');
    }
  }

  // ELIMINAR (Delete)
  Future<void> eliminar(String id) async {
    try {
      await _db.doc(id).delete();
    } catch (e) {
      throw Exception('Error al eliminar empleado: $e');
    }
  }
}
```

#### `lib/screens/home_screen.dart`
```dart
import 'package:flutter/material.dart';
import '../models/empleado.dart';
import '../services/firestore_service.dart';

class HomeScreen extends StatefulWidget {
  const HomeScreen({super.key});

  @override
  State<HomeScreen> createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  final FirestoreService _firestoreService = FirestoreService();
  final _formKey = GlobalKey<FormState>();

  // Controladores de texto para leer lo que haya en los inputs
  final TextEditingController _nombreCtrl = TextEditingController();
  final TextEditingController _edadCtrl = TextEditingController();
  final TextEditingController _puestoCtrl = TextEditingController();

  // Si esta variable es nula, estamos Creando. Si tiene algo, estamos Editando.
  String? _empleadoIdActual;
  bool _isLoading = false;

  void _guardarFormulario() async {
    if (_formKey.currentState!.validate()) {
      setState(() => _isLoading = true);

      try {
        final empleado = Empleado(
          id: _empleadoIdActual ?? '',
          nombre: _nombreCtrl.text.trim(),
          // Transformamos el string del input a un número (int)
          edad: int.parse(_edadCtrl.text.trim()),
          puesto: _puestoCtrl.text.trim(),
        );

        if (_empleadoIdActual == null) {
          await _firestoreService.crear(empleado);
          _mostrarMensaje('Empleado creado exitosamente');
        } else {
          await _firestoreService.actualizar(empleado);
          _mostrarMensaje('Empleado actualizado exitosamente');
        }
        _limpiarFormulario();
      } catch (e) {
        _mostrarMensaje(e.toString(), esError: true);
      } finally {
        setState(() => _isLoading = false);
      }
    }
  }

  void _cargarEmpleadoParaEdicion(Empleado empleado) {
    setState(() {
      _empleadoIdActual = empleado.id;
      _nombreCtrl.text = empleado.nombre;
      _edadCtrl.text = empleado.edad.toString();
      _puestoCtrl.text = empleado.puesto;
    });
  }

  void _eliminarEmpleado(String id) async {
    try {
      await _firestoreService.eliminar(id);
      _mostrarMensaje('Empleado eliminado');
      if (_empleadoIdActual == id) {
        _limpiarFormulario(); // Limpiar si borramos el que estábamos editando
      }
    } catch (e) {
      _mostrarMensaje(e.toString(), esError: true);
    }
  }

  void _limpiarFormulario() {
    setState(() {
      _empleadoIdActual = null;
      _nombreCtrl.clear();
      _edadCtrl.clear();
      _puestoCtrl.clear();
    });
  }

  void _mostrarMensaje(String mensaje, {bool esError = false}) {
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(
        content: Text(mensaje),
        backgroundColor: esError ? Colors.red : Colors.green,
      ),
    );
  }

  @override
  void dispose() {
    _nombreCtrl.dispose();
    _edadCtrl.dispose();
    _puestoCtrl.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Gestión de Empleados'),
        actions: [
          if (_empleadoIdActual != null)
            IconButton(
              icon: const Icon(Icons.cancel),
              onPressed: _limpiarFormulario,
              tooltip: 'Cancelar edición',
            ),
        ],
      ),
      body: Column(
        children: [
          // 1. FORMULARIO
          Padding(
            padding: const EdgeInsets.all(16.0),
            child: Form(
              key: _formKey,
              child: Column(
                children: [
                  TextFormField(
                    controller: _nombreCtrl,
                    decoration: const InputDecoration(labelText: 'Nombre Completo'),
                    validator: (val) => val == null || val.isEmpty ? 'Requerido' : null,
                  ),
                  TextFormField(
                    controller: _edadCtrl,
                    decoration: const InputDecoration(labelText: 'Edad'),
                    keyboardType: TextInputType.number,
                    validator: (val) {
                      if (val == null || val.isEmpty) return 'Requerido';
                      final num = int.tryParse(val);
                      if (num == null || num <= 0) return 'Edad inválida';
                      return null;
                    },
                  ),
                  TextFormField(
                    controller: _puestoCtrl,
                    decoration: const InputDecoration(labelText: 'Puesto'),
                    validator: (val) => val == null || val.isEmpty ? 'Requerido' : null,
                  ),
                  const SizedBox(height: 16),
                  SizedBox(
                    width: double.infinity,
                    child: ElevatedButton(
                      onPressed: _isLoading ? null : _guardarFormulario,
                      child: _isLoading
                          ? const CircularProgressIndicator()
                          : Text(_empleadoIdActual == null ? 'GUARDAR' : 'ACTUALIZAR'),
                    ),
                  ),
                ],
              ),
            ),
          ),
          const Divider(),
          // 2. LISTA (StreamBuilder)
          Expanded(
            child: StreamBuilder<List<Empleado>>(
              stream: _firestoreService.leerTodos(),
              builder: (context, snapshot) {
                // Manejo del estado de carga
                if (snapshot.connectionState == ConnectionState.waiting) {
                  return const Center(child: CircularProgressIndicator());
                }
                
                // Manejo de errores en la escucha
                if (snapshot.hasError) {
                  return Center(child: Text('Error: ${snapshot.error}'));
                }
                
                // Manejo de estado vacío
                final empleados = snapshot.data ?? [];
                if (empleados.isEmpty) {
                  return const Center(child: Text('No hay empleados registrados.'));
                }

                // Generación de la lista UI
                return ListView.builder(
                  itemCount: empleados.length,
                  itemBuilder: (context, index) {
                    final empleado = empleados[index];
                    return ListTile(
                      title: Text(empleado.nombre),
                      subtitle: Text('${empleado.puesto} - ${empleado.edad} años'),
                      trailing: Row(
                        mainAxisSize: MainAxisSize.min,
                        children: [
                          IconButton(
                            icon: const Icon(Icons.edit, color: Colors.blue),
                            onPressed: () => _cargarEmpleadoParaEdicion(empleado),
                          ),
                          IconButton(
                            icon: const Icon(Icons.delete, color: Colors.red),
                            onPressed: () => _eliminarEmpleado(empleado.id),
                          ),
                        ],
                      ),
                    );
                  },
                );
              },
            ),
          ),
        ],
      ),
    );
  }
}
```

#### `lib/main.dart`
```dart
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'screens/home_screen.dart';

void main() async {
  // Asegura que los bindings de Flutter estén listos antes de iniciadores asíncronos
  WidgetsFlutterBinding.ensureInitialized();
  
  // Inicializamos Firebase
  await Firebase.initializeApp();
  
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Formulario Firestore',
      theme: ThemeData(
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.teal),
        useMaterial3: true,
      ),
      home: const HomeScreen(),
    );
  }
}
```
