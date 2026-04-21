¡Hola! Me alegra mucho que estés listo para sumergirte en el desarrollo con Flutter y Firebase. Lo que te voy a presentar no es solo un conjunto de instrucciones sueltas, es una **guía de estudio completa** diseñada con una metodología profesional.

Imagina que somos un pequeño equipo de desarrollo. Para que el proyecto "CRUD de Empleados" sea un éxito, vamos a asignar roles, definir responsabilidades y seguir un flujo de trabajo ordenado. Esto es justo lo que se hace en la industria.

¡Ponte el sombrero de desarrollador y comencemos!

---

### Metodología de Trabajo: Asignación de Roles

Para construir nuestro software de manera eficiente, definiremos los siguientes roles. En tu práctica, **tú asumirás todos estos roles**, pero entender la separación te ayudará a pensar como un profesional.

| Rol | Responsabilidad | En este proyecto... |
| :--- | :--- | :--- |
| **👨‍💼 Product Owner (PO)** | Define el "qué" y el "por qué". Establece los requisitos del producto. | Decide que la app debe gestionar empleados con nombre, edad y salario. |
| **🏗️ Software Architect** | Define el "cómo" a gran escala. Elige las herramientas y la estructura del proyecto. | Decide usar Flutter para la interfaz, Firebase como backend y la arquitectura por capas que veremos. |
| **👨‍🔧 Developer (Tú)** | Ejecuta el plan. Escribe el código, integra las librerías y soluciona los problemas. | Escribirás cada línea de código Dart, conectarás Firebase y construirás las pantallas. |

### Habilidades (Skills) que Desarrollaremos

Al final de esta guía, habrás puesto en práctica estas habilidades fundamentales:
1.  **Configuración de entorno:** Usar `flutterfire configure` para conectar Flutter y Firebase de forma automática y segura .
2.  **Gestión de dependencias:** Añadir librerías esenciales como `firebase_core` y `cloud_firestore` en el archivo `pubspec.yaml`.
3.  **Arquitectura de Datos:** Diseñar una base de datos NoSQL en Firestore.
4.  **Operaciones CRUD:** Implementar la lógica para Crear, Leer, Actualizar y Eliminar documentos en la nube .
5.  **Programación Reactiva:** Usar `StreamBuilder` de Flutter para escuchar cambios en la base de datos en tiempo real .

---

### Flujo de Trabajo Paso a Paso para tu Práctica

Este es nuestro plan de batalla. Sigue cada paso en orden y no te saltes ninguno.

#### Fase 1: Cimientos del Proyecto (Duración estimada: 15 min)

**Paso 1: Creación de la Carpeta Raíz**
Abre tu terminal (línea de comandos) y crea la carpeta del proyecto usando el comando de Flutter:
```bash
flutter create crud_clinica
```
Luego, navega a la carpeta recién creada:
```bash
cd crud_clinica
```

**Paso 2: Abrir el Proyecto en Antigravity**
Abre tu editor de código. Si Antigravity es tu IDE, ábrelo desde allí. Si no, abre la carpeta `crud_clinica` en tu editor favorito (VS Code, Android Studio, etc.).

**Paso 3: Estructura de Carpetas (La Base del Arquitecto)**
Para mantener nuestro código limpio y escalable, crearemos las siguientes carpetas dentro de la carpeta `lib`. Esta es una práctica estándar en la industria .

```text
crud_clinica/
└── lib/
    ├── models/          # Para definir cómo se ve un Empleado
    ├── services/        # Para la lógica que conecta con Firebase
    ├── screens/         # Para las pantallas de nuestra app
    └── widgets/         # Para pequeños componentes reutilizables
```

#### Fase 2: Despliegue del Backend (Duración estimada: 20 min)

**Paso 4: Crear Base de Datos en Consola Firebase**
1.  Ve a [console.firebase.google.com](https://console.firebase.google.com) e inicia sesión con tu cuenta de Google.
2.  Crea un **nuevo proyecto** (ej. "Crud Clinica").
3.  Una vez creado, en el menú lateral, busca **"Firestore Database"** y haz clic en **"Crear base de datos"**.
4.  Para las reglas de seguridad, elige **"Iniciar en modo de prueba"**. Esto es perfecto para desarrollar y aprender. (Más adelante podrás hacerlas más seguras).
5.  Elige la ubicación del servidor (la más cercana a ti está bien) y haz clic en "Activar".

#### Fase 3: Integración y Código (Duración estimada: 1 hora)

**Paso 5: Instalar y Configurar Firebase (El Método Moderno)**
Este es el paso más crítico. Olvídate de la configuración manual. Usaremos la herramienta oficial de Firebase.

1.  **Instalar la herramienta FlutterFire CLI:** En tu terminal, ejecuta:
    ```bash
    dart pub global activate flutterfire_cli
    ```

2.  **Iniciar sesión en Firebase (si no lo has hecho):**
    ```bash
    firebase login
    ```

3.  **Añadir las librerías necesarias:** En el archivo `pubspec.yaml`, dentro de `dependencies:`, añade las siguientes líneas. Las librerías `firebase_core` y `cloud_firestore` son las más importantes . `firebase_auth` la añadimos como preparación para el futuro.
    ```yaml
    dependencies:
      flutter:
        sdk: flutter
      firebase_core: latest_version
      cloud_firestore: latest_version
      firebase_auth: latest_version
    ```
    *Nota: Puedes usar `latest_version` o buscar las versiones más recientes en [pub.dev](https://pub.dev).*

4.  **Configurar tu App con FlutterFire:** Este comando mágico vincula tu proyecto de Flutter con el de Firebase.
    ```bash
    flutterfire configure
    ```
    La herramienta te preguntará:
    - Selecciona el proyecto que creaste en la consola web.
    - Selecciona las plataformas (Android, iOS, Web). Para esta práctica, puedes seleccionar solo la que estés probando (ej. Windows, Android o Chrome).
    - Esto generará un archivo `lib/firebase_options.dart` automágicamente. **No lo modifiques**.

**Paso 6: Inicializar Firebase en tu App**
Ahora, vamos a escribir código. Abre el archivo `lib/main.dart` y reemplázalo con esto:

```dart
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'firebase_options.dart'; // <-- Importamos el archivo generado
import 'screens/empleado_screen.dart'; // Lo crearemos en el siguiente paso

void main() async {
  // Asegura que los bindings de Flutter estén inicializados
  WidgetsFlutterBinding.ensureInitialized();
  // Inicializa Firebase con la configuración específica de la plataforma
  await Firebase.initializeApp(
    options: DefaultFirebaseOptions.currentPlatform,
  );
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'CRUD Clínica',
      theme: ThemeData(primarySwatch: Colors.blue),
      home: const EmpleadoScreen(), // Nuestra pantalla principal
    );
  }
}
```

**Paso 7: Creando el Modelo de Datos**
Creamos un archivo `lib/models/empleado.dart` para definir cómo se ve nuestro empleado.

```dart
class Empleado {
  final String id;      // ID único que Firestore nos dará
  final String nombre;
  final int edad;
  final double salario;

  Empleado({
    required this.id,
    required this.nombre,
    required this.edad,
    required this.salario,
  });

  // Convierte un documento de Firestore en un objeto Empleado
  factory Empleado.fromFirestore(Map<String, dynamic> data, String id) {
    return Empleado(
      id: id,
      nombre: data['nombre'] ?? '',
      edad: data['edad'] ?? 0,
      salario: (data['salario'] ?? 0).toDouble(),
    );
  }

  // Convierte un objeto Empleado en un mapa para guardar en Firestore
  Map<String, dynamic> toMap() {
    return {
      'nombre': nombre,
      'edad': edad,
      'salario': salario,
    };
  }
}
```

**Paso 8: El Corazón de la App: Lógica de Servicios (CRUD)**
Creamos `lib/services/firestore_service.dart`. Esta clase se encargará de toda la comunicación con Firestore .

```dart
import 'package:cloud_firestore/cloud_firestore.dart';
import '../models/empleado.dart';

class FirestoreService {
  // Referencia a la colección 'empleados' en Firestore
  final CollectionReference _empleadosCollection =
      FirebaseFirestore.instance.collection('empleados');

  // CREATE: Añadir un nuevo empleado
  Future<void> addEmpleado(String nombre, int edad, double salario) async {
    try {
      await _empleadosCollection.add({
        'nombre': nombre,
        'edad': edad,
        'salario': salario,
      });
      print('Empleado añadido correctamente');
    } catch (e) {
      print('Error al añadir empleado: $e');
      throw Exception('Error al añadir empleado');
    }
  }

  // READ: Obtener el flujo (stream) de todos los empleados en tiempo real
  Stream<List<Empleado>> getEmpleados() {
    return _empleadosCollection.snapshots().map((snapshot) {
      return snapshot.docs.map((doc) {
        return Empleado.fromFirestore(doc.data() as Map<String, dynamic>, doc.id);
      }).toList();
    });
  }

  // UPDATE: Actualizar un empleado existente
  Future<void> updateEmpleado(String id, String nombre, int edad, double salario) async {
    try {
      await _empleadosCollection.doc(id).update({
        'nombre': nombre,
        'edad': edad,
        'salario': salario,
      });
      print('Empleado con ID $id actualizado');
    } catch (e) {
      print('Error al actualizar: $e');
      throw Exception('Error al actualizar empleado');
    }
  }

  // DELETE: Borrar un empleado
  Future<void> deleteEmpleado(String id) async {
    try {
      await _empleadosCollection.doc(id).delete();
      print('Empleado con ID $id eliminado');
    } catch (e) {
      print('Error al eliminar: $e');
      throw Exception('Error al eliminar empleado');
    }
  }
}
```

**Paso 9: La Interfaz de Usuario (UI)**
Finalmente, creamos nuestra pantalla. Crea el archivo `lib/screens/empleado_screen.dart`. Esta pantalla mostrará la lista y un diálogo para añadir/editar.

```dart
import 'package:flutter/material.dart';
import '../services/firestore_service.dart';
import '../models/empleado.dart';

class EmpleadoScreen extends StatefulWidget {
  const EmpleadoScreen({super.key});

  @override
  State<EmpleadoScreen> createState() => _EmpleadoScreenState();
}

class _EmpleadoScreenState extends State<EmpleadoScreen> {
  final FirestoreService _service = FirestoreService();
  final _formKey = GlobalKey<FormState>();
  final _nombreController = TextEditingController();
  final _edadController = TextEditingController();
  final _salarioController = TextEditingController();

  String? _editandoId; // Si no es null, significa que estamos editando

  void _limpiarFormulario() {
    _nombreController.clear();
    _edadController.clear();
    _salarioController.clear();
    setState(() {
      _editandoId = null;
    });
  }

  void _guardarEmpleado() {
    if (_formKey.currentState!.validate()) {
      final nombre = _nombreController.text;
      final edad = int.parse(_edadController.text);
      final salario = double.parse(_salarioController.text);

      if (_editandoId == null) {
        // Modo CREAR
        _service.addEmpleado(nombre, edad, salario);
      } else {
        // Modo ACTUALIZAR
        _service.updateEmpleado(_editandoId!, nombre, edad, salario);
      }
      _limpiarFormulario();
      Navigator.pop(context); // Cierra el diálogo
    }
  }

  void _editarEmpleado(Empleado empleado) {
    _editandoId = empleado.id;
    _nombreController.text = empleado.nombre;
    _edadController.text = empleado.edad.toString();
    _salarioController.text = empleado.salario.toString();
    _mostrarFormulario();
  }

  void _mostrarFormulario() {
    showDialog(
      context: context,
      barrierDismissible: false,
      builder: (context) => AlertDialog(
        title: Text(_editandoId == null ? 'Nuevo Empleado' : 'Editar Empleado'),
        content: Form(
          key: _formKey,
          child: Column(
            mainAxisSize: MainAxisSize.min,
            children: [
              TextFormField(
                controller: _nombreController,
                decoration: const InputDecoration(labelText: 'Nombre'),
                validator: (value) => value!.isEmpty ? 'Ingrese un nombre' : null,
              ),
              TextFormField(
                controller: _edadController,
                decoration: const InputDecoration(labelText: 'Edad'),
                keyboardType: TextInputType.number,
                validator: (value) => value!.isEmpty ? 'Ingrese edad' : null,
              ),
              TextFormField(
                controller: _salarioController,
                decoration: const InputDecoration(labelText: 'Salario'),
                keyboardType: TextInputType.number,
                validator: (value) => value!.isEmpty ? 'Ingrese salario' : null,
              ),
            ],
          ),
        ),
        actions: [
          TextButton(
            onPressed: () {
              _limpiarFormulario();
              Navigator.pop(context);
            },
            child: const Text('Cancelar'),
          ),
          ElevatedButton(
            onPressed: _guardarEmpleado,
            child: const Text('Guardar'),
          ),
        ],
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Gestión de Empleados'),
      ),
      body: StreamBuilder<List<Empleado>>(
        // Escuchamos los cambios en tiempo real
        stream: _service.getEmpleados(),
        builder: (context, snapshot) {
          if (snapshot.hasError) {
            return Center(child: Text('Error: ${snapshot.error}'));
          }
          if (snapshot.connectionState == ConnectionState.waiting) {
            return const Center(child: CircularProgressIndicator());
          }
          final empleados = snapshot.data!;
          if (empleados.isEmpty) {
            return const Center(child: Text('No hay empleados. Añade uno!'));
          }

          return ListView.builder(
            itemCount: empleados.length,
            itemBuilder: (context, index) {
              final emp = empleados[index];
              return ListTile(
                title: Text(emp.nombre),
                subtitle: Text('Edad: ${emp.edad} - Salario: \$${emp.salario}'),
                trailing: Row(
                  mainAxisSize: MainAxisSize.min,
                  children: [
                    IconButton(
                      icon: const Icon(Icons.edit),
                      onPressed: () => _editarEmpleado(emp),
                    ),
                    IconButton(
                      icon: const Icon(Icons.delete),
                      onPressed: () => _service.deleteEmpleado(emp.id),
                    ),
                  ],
                ),
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _mostrarFormulario,
        child: const Icon(Icons.add),
      ),
    );
  }
}
```

---

### ¡Hora de la Verdad! (Acción Final)

1.  Guarda todos los archivos.
2.  Asegúrate de tener un emulador o dispositivo conectado.
3.  En la terminal, ejecuta:
    ```bash
    flutter run
    ```

Si todo ha salido bien, la aplicación se compilará. Podrás añadir empleados con nombre, edad y salario, verlos en la lista, editarlos tocando el ícono de lápiz y eliminarlos con el ícono de la papelera. ¡Y todo esto se guardará directamente en la nube de Firebase!

Este proyecto no solo te enseña a programar, sino a **estructurar tu pensamiento** y tu trabajo como un desarrollador profesional. ¡Felicitaciones por llegar hasta aquí! Este es un gran primer paso en tu carrera.
