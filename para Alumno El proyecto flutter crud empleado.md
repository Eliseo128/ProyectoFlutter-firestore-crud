Perfecto. Voy a estructurarlo como una **práctica didáctica completa, guiada y evaluable**, pensada para alumnos de nivel principiante–intermedio en Flutter con Firebase.

---

# 📘 PRÁCTICA DIDÁCTICA

## CRUD de Empleados con Flutter + Firestore

---

## 🎯 Objetivo de aprendizaje

El alumno será capaz de:

* Crear un proyecto en Flutter
* Configurar Firebase y Firestore
* Implementar operaciones CRUD (Crear, Leer, Actualizar, Eliminar)
* Manejar datos en tiempo real con streams
* Organizar código en capas básicas (modelo, servicio, UI)

---

## 🧠 Conocimientos previos

* Dart básico (variables, clases)
* Flutter básico (widgets, Scaffold, ListView)
* Uso de VS Code o Android Studio

---

# 🧩 ETAPA 1 — Creación del proyecto

### Actividad del alumno

Ejecutar en terminal:

```bash
flutter create crudclinica
cd crudclinica
code .
```

### Evidencia esperada

* Proyecto Flutter ejecutándose con `flutter run`

---

# 🔥 ETAPA 2 — Configuración de Firebase

### Actividad del alumno

1. Acceder a Firebase Console
2. Crear proyecto: `crudclinica`
3. Activar **Cloud Firestore**
4. Crear base de datos en modo prueba
5. Registrar app Android
6. Descargar `google-services.json` y colocarlo en:

```
android/app/
```

---

# 📦 ETAPA 3 — Instalación de dependencias

### Actividad del alumno

Editar `pubspec.yaml`:

```yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^2.30.0
  cloud_firestore: ^4.15.0
```

Ejecutar:

```bash
flutter pub get
```

---

# ⚙️ ETAPA 4 — Inicialización de Firebase

### Actividad del alumno

Modificar `main.dart`:

```dart
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'screens/home_screen.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp();
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: HomeScreen(),
    );
  }
}
```

---

# 🧱 ETAPA 5 — Modelo de datos

### Actividad del alumno

Crear archivo:

```
lib/models/empleado.dart
```

```dart
class Empleado {
  String id;
  String nombre;
  int edad;
  double salario;

  Empleado({
    required this.id,
    required this.nombre,
    required this.edad,
    required this.salario,
  });

  factory Empleado.fromMap(Map<String, dynamic> data, String id) {
    return Empleado(
      id: id,
      nombre: data['nombre'],
      edad: data['edad'],
      salario: data['salario'].toDouble(),
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'nombre': nombre,
      'edad': edad,
      'salario': salario,
    };
  }
}
```

---

# 🔄 ETAPA 6 — Servicio Firestore

### Actividad del alumno

Crear:

```
lib/services/firestore_service.dart
```

```dart
import 'package:cloud_firestore/cloud_firestore.dart';
import '../models/empleado.dart';

class FirestoreService {
  final empleados =
      FirebaseFirestore.instance.collection('empleados');

  Future<void> agregar(Empleado e) {
    return empleados.add(e.toMap());
  }

  Stream<List<Empleado>> obtener() {
    return empleados.snapshots().map((snapshot) =>
        snapshot.docs
            .map((doc) => Empleado.fromMap(
                doc.data() as Map<String, dynamic>, doc.id))
            .toList());
  }

  Future<void> actualizar(Empleado e) {
    return empleados.doc(e.id).update(e.toMap());
  }

  Future<void> eliminar(String id) {
    return empleados.doc(id).delete();
  }
}
```

---

# 🖥️ ETAPA 7 — Interfaz (CRUD completo)

### Actividad del alumno

Crear:

```
lib/screens/home_screen.dart
```

(versión simplificada para aprendizaje)

```dart
import 'package:flutter/material.dart';
import '../services/firestore_service.dart';
import '../models/empleado.dart';

class HomeScreen extends StatelessWidget {
  final service = FirestoreService();

  final nombre = TextEditingController();
  final edad = TextEditingController();
  final salario = TextEditingController();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("Empleados")),
      body: Column(
        children: [

          TextField(controller: nombre, decoration: InputDecoration(labelText: "Nombre")),
          TextField(controller: edad, decoration: InputDecoration(labelText: "Edad")),
          TextField(controller: salario, decoration: InputDecoration(labelText: "Salario")),

          ElevatedButton(
            onPressed: () {
              service.agregar(Empleado(
                id: '',
                nombre: nombre.text,
                edad: int.parse(edad.text),
                salario: double.parse(salario.text),
              ));
            },
            child: Text("Agregar"),
          ),

          Expanded(
            child: StreamBuilder<List<Empleado>>(
              stream: service.obtener(),
              builder: (context, snapshot) {
                if (!snapshot.hasData) return CircularProgressIndicator();

                final lista = snapshot.data!;

                return ListView.builder(
                  itemCount: lista.length,
                  itemBuilder: (_, i) {
                    final e = lista[i];

                    return ListTile(
                      title: Text(e.nombre),
                      subtitle: Text("${e.edad} - \$${e.salario}"),
                      trailing: IconButton(
                        icon: Icon(Icons.delete),
                        onPressed: () => service.eliminar(e.id),
                      ),
                    );
                  },
                );
              },
            ),
          )
        ],
      ),
    );
  }
}
```

---

# 🧪 ETAPA 8 — Pruebas

### Actividad del alumno

* Agregar empleados
* Ver lista en tiempo real
* Eliminar registros

### Evidencia

* Captura de pantalla funcionando
* Base de datos en Firestore con datos

---

# 📊 RÚBRICA DE EVALUACIÓN

| Criterio                      | Puntos |
| ----------------------------- | ------ |
| Proyecto creado correctamente | 10     |
| Firebase configurado          | 20     |
| Modelo implementado           | 15     |
| CRUD funcional                | 30     |
| Interfaz funcional            | 15     |
| Pruebas realizadas            | 10     |

---

# 🚀 ACTIVIDADES DE EXTENSIÓN

Para alumnos avanzados:

* Implementar **actualización (update con diálogo)**
* Validaciones con `Form`
* Separar UI en widgets
* Agregar animaciones (en lugar de “antigravity”)

---

# 🧑‍🏫 NOTA DIDÁCTICA (docente)

Este ejercicio permite trabajar:

* Arquitectura básica en Flutter
* Persistencia en la nube
* Pensamiento CRUD
* Flujo de datos reactivo (StreamBuilder)

---

Si quieres, puedo en el siguiente paso convertir esto en:

* **Guía imprimible tipo PDF**
* **Examen práctico basado en este proyecto**
* **Versión con arquitectura profesional (MVVM o Clean Architecture)**
