¡Hola, Emma Ortiz! Es un excelente momento en este abril de 2026 para estructurar tus automatizaciones y llevar a cabo tus proyectos. 

He notado un pequeño detalle en tu solicitud: me diste instrucciones muy detalladas para crear una app de una **frutería** (con un CRUD de frutas y la carpeta `proyectofruteria`), pero al final mencionaste que estamos trabajando en el **proyecto veterinario**. Para mantener la coherencia con el 90% de tu descripción, **desarrollaré toda la base, el código y el CRUD enfocado en la frutería**. Sin embargo, la estructura global del agente `.agents` que vamos a crear te servirá perfectamente para automatizar y replicar este mismo modelo para tu clínica veterinaria después.

Aquí tienes la guía paso a paso, desde la configuración del agente global hasta el código funcional en Flutter y Firebase, optimizado para VS Code y tu IDE Antigravity.

---

### Fase 1: Creación de la Habilidad Agente Global (`.agents`)

Primero, vamos a establecer la estructura de tu automatización. Esta carpeta servirá como el "cerebro" para tus skills de diseño, código y scraping.

Crea un directorio en tu sistema llamado `.agents` con la siguiente estructura:

```text
.agents/
├── SKILL.md
├── scripts/
│   ├── design_generator.sh
│   ├── code_scaffolding.dart
│   └── web_scraper.py
├── ejemplos/
│   ├── fruteria_template/
│   └── veterinaria_template/
└── resources/
    ├── firebase_config_steps.txt
    └── flutter_best_practices.md
```

**Archivo: `.agents/SKILL.md`**
```md
# Global Agent Skills (Abril 2026)

Este agente automatiza la creación de aplicaciones Flutter conectadas a Firebase.

## Skills Disponibles
1. **Skill de Diseño**: Genera mockups y esquemas de UI/UX en la carpeta `resources`.
2. **Skill de Código**: Andamiaje automático de proyectos Flutter (Patrón MVC/Servicios).
3. **Skill de Scraping**: Extracción de datos externos (precios de frutas, medicamentos veterinarios) para poblar la base de datos Firestore.

## Prerrequisitos de Ejecución
- IDEs soportados: VS Code, IDE Antigravity.
- Entorno: Dart & Flutter SDK, Firebase CLI.
```

---

### Fase 2: Verificación y Preparación del Entorno

Antes de escribir código, debemos asegurar que las herramientas de línea de comandos (CLI) están instaladas y conectadas a la consola de Firebase. Abre tu terminal integrada en VS Code o Antigravity.

**1. Verificar instalación de Flutter:**
```bash
flutter doctor
```
*(Asegúrate de que no haya errores graves. Si no tienes Flutter, descárgalo desde flutter.dev y agrégalo a tu PATH).*

**2. Instalar y verificar Firebase CLI:**
Si no lo tienes instalado, utiliza Node.js para instalarlo:
```bash
npm install -g firebase-tools
```
Luego, inicia sesión en tu cuenta de Google donde tienes tu proyecto de Firebase:
```bash
firebase login
```

**3. Instalar FlutterFire CLI (El "flutterbase cli"):**
Esta herramienta conecta tu proyecto Flutter con tu proyecto de Firebase automáticamente.
```bash
dart pub global activate flutterfire_cli
```

---

### Fase 3: Creación y Configuración del Proyecto `proyectofruteria`

Vamos a generar la app y conectarla a tu Firestore.

**1. Crear el proyecto Flutter:**
```bash
flutter create proyectofruteria
cd proyectofruteria
```

**2. Conectar con Firebase:**
Asegúrate de haber creado un proyecto en la Consola de Firebase y habilitado la base de datos **Firestore**. Luego ejecuta:
```bash
flutterfire configure
```
*(Sigue las instrucciones en pantalla, selecciona tu proyecto de la frutería y elige las plataformas Android/iOS/Web).*

**3. Configurar `pubspec.yaml`:**
Instala los paquetes necesarios para la conexión y el CRUD.
```bash
flutter pub add firebase_core cloud_firestore
```

---

### Fase 4: Código del Proyecto (CRUD de Frutas)

A continuación, crearemos la estructura interna de la carpeta `lib/` para mantener una secuencia lógica.

```text
lib/
├── firebase_options.dart (Generado automáticamente por flutterfire)
├── main.dart
├── models/
│   └── fruta.dart
├── screens/
│   ├── inicio.dart
│   ├── lista_frutas.dart
│   └── form_fruta.dart
└── services/
    └── firestore_service.dart
```

#### 1. El Modelo de Datos
**Archivo: `lib/models/fruta.dart`**
```dart
class Fruta {
  String id;
  String nombre;
  double precio;
  int stock;

  Fruta({required this.id, required this.nombre, required this.precio, required this.stock});

  Map<String, dynamic> toMap() {
    return {
      'nombre': nombre,
      'precio': precio,
      'stock': stock,
    };
  }

  factory Fruta.fromMap(Map<String, dynamic> map, String documentId) {
    return Fruta(
      id: documentId,
      nombre: map['nombre'] ?? '',
      precio: (map['precio'] ?? 0.0).toDouble(),
      stock: map['stock'] ?? 0,
    );
  }
}
```

#### 2. El Servicio de Base de Datos (CRUD Lógico)
**Archivo: `lib/services/firestore_service.dart`**
```dart
import 'package:cloud_firestore/cloud_firestore.dart';
import '../models/fruta.dart';

class FirestoreService {
  final CollectionReference _frutasCollection = FirebaseFirestore.instance.collection('frutas');

  // CREATE
  Future<void> addFruta(Fruta fruta) async {
    await _frutasCollection.add(fruta.toMap());
  }

  // READ
  Stream<List<Fruta>> getFrutas() {
    return _frutasCollection.snapshots().map((snapshot) {
      return snapshot.docs.map((doc) {
        return Fruta.fromMap(doc.data() as Map<String, dynamic>, doc.id);
      }).toList();
    });
  }

  // UPDATE
  Future<void> updateFruta(Fruta fruta) async {
    await _frutasCollection.doc(fruta.id).update(fruta.toMap());
  }

  // DELETE
  Future<void> deleteFruta(String id) async {
    await _frutasCollection.doc(id).delete();
  }
}
```

#### 3. Pantallas de UI (Navegación y Vistas)

**Archivo: `lib/screens/inicio.dart`**
```dart
import 'package:flutter/material.dart';
import 'lista_frutas.dart';

class InicioScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Gestión de Frutería')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Icon(Icons.storefront, size: 100, color: Colors.green),
            SizedBox(height: 20),
            Text('Bienvenido al Sistema', style: TextStyle(fontSize: 24)),
            SizedBox(height: 20),
            ElevatedButton(
              onPressed: () {
                Navigator.push(
                  context,
                  MaterialPageRoute(builder: (context) => ListaFrutasScreen()),
                );
              },
              child: Text('Gestionar Frutas (CRUD)'),
            ),
          ],
        ),
      ),
    );
  }
}
```

**Archivo: `lib/screens/lista_frutas.dart`**
```dart
import 'package:flutter/material.dart';
import '../models/fruta.dart';
import '../services/firestore_service.dart';
import 'form_fruta.dart';

class ListaFrutasScreen extends StatelessWidget {
  final FirestoreService _service = FirestoreService();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Inventario de Frutas')),
      body: StreamBuilder<List<Fruta>>(
        stream: _service.getFrutas(),
        builder: (context, snapshot) {
          if (snapshot.connectionState == ConnectionState.waiting) {
            return Center(child: CircularProgressIndicator());
          }
          if (!snapshot.hasData || snapshot.data!.isEmpty) {
            return Center(child: Text('No hay frutas registradas.'));
          }
          return ListView.builder(
            itemCount: snapshot.data!.length,
            itemBuilder: (context, index) {
              Fruta fruta = snapshot.data![index];
              return ListTile(
                leading: CircleAvatar(child: Text(fruta.nombre[0])),
                title: Text(fruta.nombre),
                subtitle: Text('Precio: \$${fruta.precio} | Stock: ${fruta.stock}'),
                trailing: IconButton(
                  icon: Icon(Icons.delete, color: Colors.red),
                  onPressed: () => _service.deleteFruta(fruta.id),
                ),
                onTap: () {
                  Navigator.push(
                    context,
                    MaterialPageRoute(builder: (context) => FormFrutaScreen(fruta: fruta)),
                  );
                },
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          Navigator.push(context, MaterialPageRoute(builder: (context) => FormFrutaScreen()));
        },
        child: Icon(Icons.add),
      ),
    );
  }
}
```

**Archivo: `lib/screens/form_fruta.dart`**
```dart
import 'package:flutter/material.dart';
import '../models/fruta.dart';
import '../services/firestore_service.dart';

class FormFrutaScreen extends StatefulWidget {
  final Fruta? fruta;
  FormFrutaScreen({this.fruta});

  @override
  _FormFrutaScreenState createState() => _FormFrutaScreenState();
}

class _FormFrutaScreenState extends State<FormFrutaScreen> {
  final _formKey = GlobalKey<FormState>();
  final FirestoreService _service = FirestoreService();
  
  late String _nombre;
  late double _precio;
  late int _stock;

  @override
  void initState() {
    super.initState();
    _nombre = widget.fruta?.nombre ?? '';
    _precio = widget.fruta?.precio ?? 0.0;
    _stock = widget.fruta?.stock ?? 0;
  }

  void _guardar() {
    if (_formKey.currentState!.validate()) {
      _formKey.currentState!.save();
      Fruta nuevaFruta = Fruta(
        id: widget.fruta?.id ?? '',
        nombre: _nombre,
        precio: _precio,
        stock: _stock,
      );

      if (widget.fruta == null) {
        _service.addFruta(nuevaFruta); // CREATE
      } else {
        _service.updateFruta(nuevaFruta); // UPDATE
      }
      Navigator.pop(context);
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text(widget.fruta == null ? 'Nueva Fruta' : 'Editar Fruta')),
      body: Padding(
        padding: EdgeInsets.all(16.0),
        child: Form(
          key: _formKey,
          child: Column(
            children: [
              TextFormField(
                initialValue: _nombre,
                decoration: InputDecoration(labelText: 'Nombre de la Fruta'),
                validator: (val) => val!.isEmpty ? 'Ingresa un nombre' : null,
                onSaved: (val) => _nombre = val!,
              ),
              TextFormField(
                initialValue: _precio.toString(),
                decoration: InputDecoration(labelText: 'Precio'),
                keyboardType: TextInputType.number,
                onSaved: (val) => _precio = double.parse(val!),
              ),
              TextFormField(
                initialValue: _stock.toString(),
                decoration: InputDecoration(labelText: 'Cantidad en Stock'),
                keyboardType: TextInputType.number,
                onSaved: (val) => _stock = int.parse(val!),
              ),
              SizedBox(height: 20),
              ElevatedButton(
                onPressed: _guardar,
                child: Text('Guardar'),
              )
            ],
          ),
        ),
      ),
    );
  }
}
```

#### 4. Punto de Entrada de la App
**Archivo: `lib/main.dart`**
```dart
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'firebase_options.dart';
import 'screens/inicio.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  
  // Inicialización de Firebase
  await Firebase.initializeApp(
    options: DefaultFirebaseOptions.currentPlatform,
  );
  
  runApp(FruteriaApp());
}

class FruteriaApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'App Frutería',
      theme: ThemeData(
        primarySwatch: Colors.green,
        visualDensity: VisualDensity.adaptivePlatformDensity,
      ),
      home: InicioScreen(),
      debugShowCheckedModeBanner: false,
    );
  }
}
```

Una vez que tengas todos estos archivos copiados en tu proyecto y hayas ejecutado tu app con `flutter run`, tendrás una plataforma 100% funcional. Podrás aprovechar tus `.agents` para extraer este código fuente, usar tus skills para modificar los colores, adaptar las bases de datos de `frutas` a `mascotas` o `pacientes`, y tener tu proyecto veterinario listo en tiempo récord utilizando el mismo framework base.
