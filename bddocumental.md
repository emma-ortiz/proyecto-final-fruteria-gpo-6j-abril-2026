Aquí tienes un ejemplo claro y bien estructurado usando **MongoDB** (porque maneja *bases de datos → colecciones → documentos*).

---

# 🟢 1. Crear la base de datos

```js
use fruteriaDB
```

Esto crea (o selecciona) la base de datos llamada `fruteriaDB`.

---

# 🟢 2. Colecciones principales

Para una frutería, podrías tener:

* `productos`
* `clientes`
* `ventas`
* `proveedores`

---

# 🟢 3. Ejemplo de documentos con atributos y tipos de datos

## 🍎 Colección: productos

```js
db.productos.insertOne({
  nombre: "Manzana",
  categoria: "Fruta",
  precio: 32.5,              // Number (double)
  stock: 150,                // Number (int)
  unidad: "kg",              // String
  fechaIngreso: new Date(),  // Date
  proveedorId: ObjectId(),   // ObjectId (referencia)
  activo: true               // Boolean
})
```

---

## 👤 Colección: clientes

```js
db.clientes.insertOne({
  nombre: "Juan Pérez",
  telefono: "6561234567",   // String
  email: "juan@gmail.com",  // String
  direccion: {
    calle: "Av. Juárez",
    numero: 123,
    ciudad: "Ciudad Juárez"
  },                        // Objeto embebido
  fechaRegistro: new Date(),
  puntos: 50                // Number
})
```

---

## 🧾 Colección: ventas

```js
db.ventas.insertOne({
  clienteId: ObjectId(),   // referencia a cliente
  productos: [
    {
      productoId: ObjectId(),
      cantidad: 2,          // Number
      precioUnitario: 32.5  // Number
    }
  ],
  total: 65.0,              // Number
  fecha: new Date(),        // Date
  metodoPago: "efectivo"    // String
})
```

---

## 🚚 Colección: proveedores

```js
db.proveedores.insertOne({
  nombre: "Distribuidora El Campo",
  telefono: "6569876543",
  direccion: "Chihuahua, México",
  productosSuministrados: ["Manzana", "Plátano"], // Array
  activo: true
})
```

---

# 🟢 4. Tipos de datos usados (MongoDB)

* **String** → texto (`"Manzana"`)
* **Number** → números (`32.5`, `150`)
* **Boolean** → `true / false`
* **Date** → fechas (`new Date()`)
* **ObjectId** → identificadores únicos
* **Array** → listas (`[]`)
* **Object** → subdocumentos (`{}`)

---

# 🟢 5. Relación entre colecciones

* `ventas.clienteId` → referencia a `clientes`
* `ventas.productos.productoId` → referencia a `productos`
* `productos.proveedorId` → referencia a `proveedores`

---

# 🟢 6. Ejemplo de consulta

```js
db.productos.find({ stock: { $lt: 50 } })
```

👉 Muestra productos con poco inventario.

---

Si quieres, puedo ayudarte a:

* Diseñarlo como **diagrama (tipo entidad-relación)**
* Convertirlo a **SQL (MySQL)**
* O conectarlo a una página web como la que estás haciendo 👀
