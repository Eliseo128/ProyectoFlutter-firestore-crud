Voy a ser directo: mezclar “antigravity” (librería Python) con Flutter (Dart) no es viable en un mismo runtime. Si tu intención es usar efectos visuales tipo “antigravity”, en Flutter se resuelve con animaciones nativas (por ejemplo `AnimatedContainer`, `Transform`, `Hero`, etc.), no con esa librería. A partir de aquí, te propongo un plan sólido y completamente funcional para un CRUD de empleados con Firebase.

---

# 🧩 PLAN DE TRABAJO — CRUD EMPLEADOS (Flutter + Firebase Firestore)

## 1. Estructura inicial del proyecto

```bash
flutter create crudclinica
cd crudclinica
code .
```

Estructura sugerida:

```
lib/
 ├── main.dart
 ├── services/
 │    └── firestore_service.dart
 ├── models/
 │    └── empleado.dart
 ├── screens/
 │    └── home_screen.dart
```

---

## 2. Configuración en Firebase (Firestore)

1. Ir a Firebase Console
2. Crear proyecto → `crudclinica`
3. Activar **Cloud Firestore**
4. Crear base de datos en modo prueba
5. Registrar app Flutter (Android/iOS)
6. Descargar archivo:

   * `google-services.json` (Android)
   * `GoogleService-Info.plist` (iOS)

---

## 3. Librerías necesarias

Editar `pubspec.yaml`:

```yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^2.30.0
  cloud_firestore: ^4.15.0
```

Instalar:

```bash
flutter pub get
```

---

## 4. Inicialización de Firebase

### main.dart

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
      debugShowCheckedModeBanner: false,
      home: HomeScreen(),
    );
  }
}
```

---

## 5. Modelo de datos (Empleado)

### models/empleado.dart

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

## 6. Servicio Firestore (CRUD)

### services/firestore_service.dart

```dart
import 'package:cloud_firestore/cloud_firestore.dart';
import '../models/empleado.dart';

class FirestoreService {
  final CollectionReference empleadosRef =
      FirebaseFirestore.instance.collection('empleados');

  // CREATE
  Future<void> agregarEmpleado(Empleado emp) {
    return empleadosRef.add(emp.toMap());
  }

  // READ
  Stream<List<Empleado>> obtenerEmpleados() {
    return empleadosRef.snapshots().map((snapshot) {
      return snapshot.docs.map((doc) {
        return Empleado.fromMap(
            doc.data() as Map<String, dynamic>, doc.id);
      }).toList();
    });
  }

  // UPDATE
  Future<void> actualizarEmpleado(Empleado emp) {
    return empleadosRef.doc(emp.id).update(emp.toMap());
  }

  // DELETE
  Future<void> eliminarEmpleado(String id) {
    return empleadosRef.doc(id).delete();
  }
}
```

---

## 7. Interfaz principal (CRUD completo)

### screens/home_screen.dart

```dart
import 'package:flutter/material.dart';
import '../services/firestore_service.dart';
import '../models/empleado.dart';

class HomeScreen extends StatelessWidget {
  final FirestoreService service = FirestoreService();

  final TextEditingController nombreCtrl = TextEditingController();
  final TextEditingController edadCtrl = TextEditingController();
  final TextEditingController salarioCtrl = TextEditingController();

  void limpiar() {
    nombreCtrl.clear();
    edadCtrl.clear();
    salarioCtrl.clear();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("CRUD Empleados")),
      body: Column(
        children: [

          // FORMULARIO
          Padding(
            padding: EdgeInsets.all(10),
            child: Column(
              children: [
                TextField(controller: nombreCtrl, decoration: InputDecoration(labelText: "Nombre")),
                TextField(controller: edadCtrl, decoration: InputDecoration(labelText: "Edad")),
                TextField(controller: salarioCtrl, decoration: InputDecoration(labelText: "Salario")),
                
                ElevatedButton(
                  onPressed: () {
                    final emp = Empleado(
                      id: '',
                      nombre: nombreCtrl.text,
                      edad: int.parse(edadCtrl.text),
                      salario: double.parse(salarioCtrl.text),
                    );

                    service.agregarEmpleado(emp);
                    limpiar();
                  },
                  child: Text("Agregar"),
                ),
              ],
            ),
          ),

          // LISTA
          Expanded(
            child: StreamBuilder<List<Empleado>>(
              stream: service.obtenerEmpleados(),
              builder: (context, snapshot) {
                if (!snapshot.hasData) return CircularProgressIndicator();

                final empleados = snapshot.data!;

                return ListView.builder(
                  itemCount: empleados.length,
                  itemBuilder: (context, index) {
                    final emp = empleados[index];

                    return ListTile(
                      title: Text(emp.nombre),
                      subtitle: Text("Edad: ${emp.edad} | \$${emp.salario}"),
                      
                      trailing: Row(
                        mainAxisSize: MainAxisSize.min,
                        children: [

                          // UPDATE
                          IconButton(
                            icon: Icon(Icons.edit),
                            onPressed: () {
                              nombreCtrl.text = emp.nombre;
                              edadCtrl.text = emp.edad.toString();
                              salarioCtrl.text = emp.salario.toString();

                              showDialog(
                                context: context,
                                builder: (_) => AlertDialog(
                                  title: Text("Actualizar"),
                                  actions: [
                                    TextButton(
                                      onPressed: () {
                                        final actualizado = Empleado(
                                          id: emp.id,
                                          nombre: nombreCtrl.text,
                                          edad: int.parse(edadCtrl.text),
                                          salario: double.parse(salarioCtrl.text),
                                        );

                                        service.actualizarEmpleado(actualizado);
                                        limpiar();
                                        Navigator.pop(context);
                                      },
                                      child: Text("Guardar"),
                                    )
                                  ],
                                ),
                              );
                            },
                          ),

                          // DELETE
                          IconButton(
                            icon: Icon(Icons.delete),
                            onPressed: () {
                              service.eliminarEmpleado(emp.id);
                            },
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

---

# ⚙️ Resultado final

✔ CRUD completo funcional
✔ Conexión en tiempo real con Firestore
✔ Arquitectura básica por capas (model / service / UI)
✔ Código listo para ejecutar en VS Code o Android Studio

---

# ⚠️ Recomendaciones técnicas

* Validar entradas (`tryParse`) para evitar errores
* Implementar `Form` y `TextFormField` con validaciones
* Agregar autenticación con Firebase Auth
* Separar lógica con Provider o Riverpod (nivel intermedio)

---

Si quieres, en el siguiente paso puedo:

* Convertir este proyecto en **arquitectura profesional (MVVM o Clean Architecture)**
* Agregar **login con Firebase Auth**
* O integrarlo como **práctica didáctica paso a paso para tus alumnos**
