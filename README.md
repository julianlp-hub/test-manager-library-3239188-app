# test-manager-library-3239188-app

Frontend de *Manager Library* en **React**. Consume la API Laravel del repo `api`
respetando `api-contract.md` del spec. Este repositorio entrega el **diseño del
frontend** guiado por el spec (SDD): pantallas, rutas, roles y consumo de la API.

## Stack

| Elemento | Decisión |
|---|---|
| Framework | React 18 + Vite |
| Rutas | React Router |
| Peticiones | `fetch` envuelto en un cliente único (`apiClient`) |
| Estado de sesión | Contexto de React; token en memoria (y `sessionStorage` para recargar) |
| Estilos | CSS simple, sin librería de UI |
| Variable de entorno | `VITE_API_URL` (ej. `http://localhost:8000/api/v1`) |

## Rutas del frontend

| Ruta | Pantalla | Roles |
|---|---|---|
| /login | Inicio de sesión | público |
| /books | Catálogo y búsqueda | admin, librarian |
| /books/new, /books/:id/edit | Formulario de libro | admin |
| /members | Socios | admin, librarian |
| /loans/new | Prestar | admin, librarian |
| /loans | Listado de préstamos y devolución | admin, librarian |
| /overdue | Libros vencidos | admin, librarian |
| /reports | Más prestados | admin |
| /users/new | Crear bibliotecario | admin |

El detalle de cada pantalla está en [`docs/pantallas.md`](docs/pantallas.md).

## Reglas del frontend que vienen del spec

- **Sesión:** token JWT de 8 horas. Si la API responde 401, se cierra la sesión y se vuelve a `/login`.
- **Roles:** el menú oculta lo que el rol no puede usar, pero la autoridad es la API (403).
- **Errores:** la API siempre responde `{ code, message, details }`. El cliente muestra
  `message` (ya viene en español) y marca los campos de `details`.
- **Portadas:** `GET /books/{id}/cover` exige token, así que no sirve un `<img src>` directo;
  se pide con `fetch` y se muestra con `URL.createObjectURL`. Si responde 404, se pinta un marcador.
- **Fechas:** la API entrega UTC con `Z`; el frontend las formatea a hora local solo al mostrarlas.
- **Datos personales:** el reporte no muestra socios ni bibliotecarios (CA-10.3).

## Cómo se ejecutaría

```
npm install
cp .env.example .env     # define VITE_API_URL
npm run dev
```

## Evidencia (T-22)

Recorrido manual de punta a punta: login → buscar libro → crear socio → prestar →
ver vencidos → devolver → reporte (como admin).

## Estado

Diseño documentado. Ver `docs/pantallas.md`.