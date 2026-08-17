1. Definición del Dominio del Problema
Nombre del Sistema: MateroStore (E-commerce en artículos materos).

Contexto y Problema:
El sistema gestiona la venta de artículos divididos por categorías (Mates, Termos, Latas, Canastas, Combos y Yerbas). Debe registrar usuarios, administrar un catálogo unificado de productos clasificados por estas líneas y procesar las órdenes de compra.

Rol de la Base de Datos (MongoDB):
La base de datos orientada a documentos nos permite agrupar todo el catálogo en una única colección (products), utilizando la categoría como un atributo para diferenciar si se trata de un mate, termo, lata, canasta, combo o yerba, y relacionarlo de manera eficiente con los usuarios y las órdenes.

2. Modelado Conceptual Orientado a Documentos
Colección: users

{
  "_id": ObjectId("user123"),
  "name": "Julieta Calderón",
  "email": "julicalderon55@gmail.com",
  "shipping_address": "Liniers 123, Mendoza"
}

Colección: products (Catálogo unificado por categorías)
Una única entidad que agrupa los artículos, permitiendo clasificarlos mediante el campo category según las secciones de la tienda (Mates, Termos, Latas, Canastas, Combos, Yerbas).

{
  "_id": ObjectId("prod001"),
  "name": "Mate Pampa Térmico",
  "category": "Mates",
  "price": 2500.00,
  "stock": 30
}

Colección: orders
Registra la venta aplicando la decisión arquitectónica clave: se referencia al usuario, pero se anidan (embedded) los datos del producto dentro de los ítems para congelar el precio histórico al momento de la compra.

{
  "_id": ObjectId("ord999"),
  "user_id": ObjectId("user123"),
  "items": [
    {
      "product_id": ObjectId("prod001"),
      "product_name": "Mate Pampa Térmico",
      "category": "Mates",
      "quantity": 1,
      "price_at_purchase": 2500.00
    }
  ],
  "total_amount": 2500.00,
  "status": "pending"
}

3. Fundamentación de la Lógica NoSQL
Catálogo unificado (products): Manejar una única colección para todos los artículos simplifica la estructura de la base de datos, permitiendo filtrar dinámicamente por categorías como Mates, Termos, Latas, Canastas, Combos o Yerbas sin fragmentar la información.

Anidación (embedded documents) en orders: Los ítems de la compra se anidan para que la información del producto y su precio queden guardados de forma histórica e inalterable. Esto permite consultar el detalle completo de la venta mediante una única lectura.

Referencias (references):

El user_id en la orden referencia a la colección de usuarios para evitar duplicar información personal.

El product_id dentro de los ítems actúa como referencia para identificar el artículo original al momento de actualizar el stock.

