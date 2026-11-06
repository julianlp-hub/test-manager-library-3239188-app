# Pantallas del frontend

Cada pantalla indica qué endpoint consume, qué historia de usuario cubre y qué
errores de la API debe manejar.

## 1. Login (/login) · HU-01
- Campos: usuario y clave.
- Consume: `POST /auth/login`.
- Éxito: guarda `access_token` y `role`, y redirige a `/books`.
- Error 401 `INVALID_CREDENTIALS`: muestra el mensaje genérico; no indica si falló usuario o clave.

## 2. Catálogo (/books) · HU-04
- Buscador por texto (título o autor), filtro por categoría, paginación (`size` máx. 50).
- Consume: `GET /books`, `GET /categories`.
- Muestra título, autor, categoría, `available_copies / total_copies` y portada.
- Admin: botones crear, editar, quitar y subir portada.

## 3. Formulario de libro (/books/new, /books/:id/edit) · HU-03 (solo admin)
- Campos: título, autor, ISBN (13 dígitos), categoría, total de ejemplares (>= 1).
- Consume: `POST /books`, `PUT /books/{id}`, `PUT/DELETE /books/{id}/cover`, `DELETE /books/{id}`.
- Portada: solo JPG o PNG, máx. 2 MB (validación previa en el cliente; la API valida por contenido).
- Errores: 409 `ISBN_TAKEN`, 409 `BOOK_HAS_OPEN_LOANS`, 422 `VALIDATION_ERROR`.

## 4. Socios (/members) · HU-05
- Lista con búsqueda por nombre o documento, y formulario de alta.
- Consume: `GET /members`, `GET /members/{id}`, `POST /members`.
- Detalle muestra `active_loans` (libros sin devolver).
- Error: 409 `DOCUMENT_TAKEN`. No hay botón de eliminar (CA-05.3).

## 5. Prestar (/loans/new) · HU-06
- Flujo: elegir socio, agregar de 1 a 3 libros (sin repetir), confirmar.
- Consume: `GET /members`, `GET /books`, `POST /loans`.
- Muestra al confirmar: fecha de vencimiento (`due_at`) de cada línea.
- Errores que debe explicar al bibliotecario:
  - 409 `BOOK_NOT_AVAILABLE`: no se registró nada.
  - 409 `MEMBER_HAS_OVERDUE`: el socio debe devolver primero.
  - 422 `MEMBER_LIMIT_EXCEEDED`: indica el conteo actual.
  - 422 `DUPLICATE_BOOK_IN_LOAN`.

## 6. Préstamos y devolución (/loans) · HU-07, HU-08
- Lista con filtros: rango de fechas, estado (`open`, `overdue`, `closed`) y socio; orden por fecha descendente.
- Consume: `GET /loans`, `GET /loans/{id}`, `POST /loans/{loan_id}/items/{item_id}/return`.
- Cada línea sin devolver tiene un botón "Devolver" (una línea a la vez).
- Error 409 `ITEM_ALREADY_RETURNED`: refresca la línea.
- El estado se muestra como insignia; viene calculado de la API, no se deduce en el cliente.

## 7. Vencidos (/overdue) · HU-09
- Tabla: libro, socio, fecha de vencimiento, días de retraso. Orden por más retraso.
- Consume: `GET /loans/overdue`.
- Aquí sí se muestra el nombre del socio (listado operativo).

## 8. Reporte (/reports) · HU-10 (solo admin)
- Dos fechas obligatorias (desde, hasta), ambas inclusive.
- Consume: `GET /reports/most-borrowed`.
- Tabla: libro, categoría, veces prestado. Rango sin préstamos: mensaje de lista vacía, no error.
- Error 422 `INVALID_DATE_RANGE`: fin anterior al inicio. No muestra socios ni bibliotecarios.

## 9. Crear bibliotecario (/users/new) · HU-02 (solo admin)
- Campos: usuario y clave. El rol es fijo: `librarian`.
- Consume: `POST /users`.
- Error 409 `USERNAME_TAKEN`. La clave nunca se muestra ni se guarda en el cliente.

## 10. Estructura de carpetas propuesta

```
src/
  api/        apiClient.js, books.js, loans.js ...
  auth/       AuthContext.jsx, RequireRole.jsx
  pages/      Login, Books, BookForm, Members, NewLoan, Loans, Overdue, Report, NewUser
  components/ Navbar, Pagination, ErrorMessage, CoverImage
```

## 11. Trazabilidad

| Pantalla | HU | Tarea |
|---|---|---|
| Login | HU-01 | T-22 |
| Catálogo y formulario | HU-03, HU-04 | T-22 |
| Socios | HU-05 | T-22 |
| Prestar | HU-06 | T-22 |
| Préstamos y devolución | HU-07, HU-08 | T-22 |
| Vencidos | HU-09 | T-22 |
| Reporte | HU-10 | T-22 |
| Crear bibliotecario | HU-02 | T-22 |