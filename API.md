# Tatadata API

<div align="center">

**API de consultas de datos (RENIEC, DNI, antecedentes y más) para Perú.**

[![Base URL](https://img.shields.io/badge/Base%20URL-https%3A%2F%2Ftatadata.lat-8b5cf6?style=flat-square)](https://tatadata.lat)
[![Auth](https://img.shields.io/badge/Auth-Bearer%20Token-22c55e?style=flat-square)](#autenticación)
[![Formato](https://img.shields.io/badge/Formato-JSON-3b82f6?style=flat-square)](#respuesta)
[![Idioma](https://img.shields.io/badge/Idioma-Español-ef4444?style=flat-square)](#)

Consulta datos por DNI, busca personas por nombre, obtén el DNI completo (foto, firma y huellas incluidas) y más. Todo desde una API simple con token.

</div>

---

## Índice

- [Qué es](#qué-es)
- [Requisitos previos](#requisitos-previos)
- [Autenticación](#autenticación)
- [Paso a paso (guía rápida)](#paso-a-paso-guía-rápida)
- [Endpoints](#endpoints)
- [Ejemplos de código](#ejemplos-de-código)
- [Formato de respuesta](#formato-de-respuesta)
- [Errores y códigos](#errores-y-códigos)
- [Créditos y precios](#créditos-y-precios)
- [Contacto y soporte](#contacto-y-soporte)

---

## Qué es

Tatadata API te permite consultar información de **RENIEC** y otros registros del Perú mediante peticiones HTTP simples. Cada consulta devuelve datos en formato **JSON**.

### Servicios disponibles

| Servicio | Descripción |
|----------|-------------|
| **Consulta DNI básica** | DNI, nombres, apellidos, sexo y edad |
| **Consulta DNI completa** | Todos los datos del titular (dirección, estado civil, fechas, etc.) |
| **DoxV4** | DNI completo + **foto, firma y huellas** en base64 |
| **Doxi** | DNI completo con datos adicionales |
| **DNI Virtual** | Información del DNI virtual |
| **DNI Electrónico** | Información del DNI electrónico |
| **Búsqueda por nombre** | Buscar personas por nombre, apellido, edad o departamento |

---

## Requisitos previos

Antes de empezar necesitas:

1. **Una cuenta** en [tatadata.lat](https://tatadata.lat)
2. **Créditos** cargados en tu cuenta
3. **Un token API** (se genera desde el panel)

---

## Autenticación

Todas las peticiones requieren un **token API**. El token se envía de dos formas (elige una):

**Opción A — Header `Authorization`** *(recomendado)*
```
Authorization: Bearer tata_xxxxxxxxxxxxxxxx
```

**Opción B — Query param `token`**
```
?token=tata_xxxxxxxxxxxxxxxx
```

> El token se ve así: `tata_` + una cadena secreta. Trátalo como una contraseña y no lo compartas.

---

## Paso a paso (guía rápida)

### 1. Crea tu cuenta
Entra a [tatadata.lat](https://tatadata.lat) y regístrate.

### 2. Recarga créditos
Cada consulta descuenta créditos. Recarga desde la sección **"Comprar Créditos"** del panel.

### 3. Genera tu token
Ve a **`/dashboard/apis`** y haz clic en **"Crear Token"**. Copia el token que aparece (solo se muestra una vez).

### 4. Haz tu primera consulta
Reemplaza `TU_TOKEN` y el `dni` y ejecuta:

```bash
curl "https://tatadata.lat/api/tatadata/dnibasico?dni=44444444" \
  -H "Authorization: Bearer TU_TOKEN"
```

### 5. Listo
Recibirás la respuesta en JSON con los datos.

---

## Endpoints

> **Base URL:** `https://tatadata.lat`

| Método | Endpoint | Parámetros | Descripción |
|--------|----------|-----------|-------------|
| `GET` / `POST` | `/api/tatadata/dnibasico` | `dni` (8 dígitos) | DNI básico |
| `GET` / `POST` | `/api/tatadata/full` | `dni` (8 dígitos) | DNI completo |
| `GET` / `POST` | `/api/tatadata/completo` | `dni` (8 dígitos) | DNI completo |
| `GET` / `POST` | `/api/tatadata/dnidoxv4` | `dni` (8 dígitos) | DoxV4 (con foto/firma/huellas) |
| `GET` / `POST` | `/api/tatadata/dniv1` | `dni` (8 dígitos) | Doxi |
| `GET` / `POST` | `/api/tatadata/dni-virtual` | `dni` (8 dígitos) | DNI virtual |
| `GET` / `POST` | `/api/tatadata/dni-electronico` | `dni` (8 dígitos) | DNI electrónico |
| `GET` | `/api/tatadata/consulta-filtro` | ver abajo | Búsqueda por nombre |

### Parámetros de `consulta-filtro`

| Parámetro | Descripción |
|-----------|-------------|
| `dni` | DNI de 8 dígitos |
| `nombre` / `nombres` | Nombre(s) de la persona |
| `apellido_paterno` / `ap_pat` | Apellido paterno |
| `apellido_materno` / `ap_mat` | Apellido materno |
| `edad` | Edad |
| `departamento` | Departamento |

> Necesitas al menos **un filtro** para que la búsqueda funcione.

---

## Ejemplos de código

### cURL

```bash
# DNI básico
curl "https://tatadata.lat/api/tatadata/dnibasico?dni=44444444" \
  -H "Authorization: Bearer TU_TOKEN"

# DoxV4 (con biometría)
curl "https://tatadata.lat/api/tatadata/dnidoxv4?dni=44444444" \
  -H "Authorization: Bearer TU_TOKEN"

# Búsqueda por nombre
curl "https://tatadata.lat/api/tatadata/consulta-filtro?nombre=MARIA&apellido_paterno=PEREZ" \
  -H "Authorization: Bearer TU_TOKEN"
```

### Python

```python
import requests

BASE = "https://tatadata.lat"
TOKEN = "tata_xxxxxxxxxxxxxxxx"

headers = {"Authorization": f"Bearer {TOKEN}"}

resp = requests.get(f"{BASE}/api/tatadata/dnibasico", params={"dni": "44444444"}, headers=headers)
data = resp.json()

if data.get("ok"):
    persona = data["data"]
    print(persona["nombres"], persona["apellido_paterno"])
else:
    print("Error:", data.get("error"))
```

### JavaScript / Node.js

```javascript
const TOKEN = "tata_xxxxxxxxxxxxxxxx";

const resp = await fetch(
  "https://tatadata.lat/api/tatadata/dnibasico?dni=44444444",
  { headers: { Authorization: `Bearer ${TOKEN}` } }
);

const data = await resp.json();

if (data.ok) {
  console.log(data.data);
} else {
  console.error(data.error);
}
```

---

## Formato de respuesta

### Respuesta exitosa

```json
{
  "ok": true,
  "query": { "dni": "44444444" },
  "data": {
    "dni": "44444444",
    "codigo_verificacion": "5",
    "nombres": "MARIA FERNANDA",
    "apellido_paterno": "PEREZ",
    "apellido_materno": "GONZALES",
    "estado_civil": "SOLTERA",
    "direccion": "AV. LOS PROCERES 123",
    "sexo": "FEMENINO",
    "edad": 25,
    "fecha_nacimiento": "12/05/2000",
    "departamento_nacimiento": "LIMA",
    "provincia_nacimiento": "LIMA",
    "distrito_nacimiento": "SAN JUAN DE LURIGANCHO",
    "fecha_inscripcion": "20/06/2002",
    "fecha_emision": "10/08/2018",
    "fecha_caducidad": "12/05/2024",
    "nombre_madre": "ROSA GONZALES",
    "nombre_padre": "CARLOS PEREZ",
    "foto": "/9j/4AAQSkZJRg... (base64)",
    "firma": "/9j/4AAQSkZJRg... (base64)",
    "huella_derecha": "/9j/4AAQSkZJRg... (base64)",
    "huella_izquierda": "/9j/4AAQSkZJRg... (base64)"
  }
}
```

> Los campos `foto`, `firma`, `huella_derecha` y `huella_izquierda` vienen en **base64** (JPEG). Para mostrarlos: `data:image/jpeg;base64,<valor>`.

### Respuesta de error

```json
{
  "ok": false,
  "error": "No tienes créditos suficientes."
}
```

---

## Errores y códigos

| Código | Significado |
|--------|-------------|
| `200` | Consulta exitosa |
| `400` | DNI inválido o faltan parámetros |
| `401` | Token inválido o expirado |
| `402` | Créditos insuficientes |
| `500` | Error interno |
| `502` | Error del proveedor |
| `503` | Servicio no disponible |

---

## Créditos y precios

- Cada consulta **descuenta créditos** de tu cuenta.
- Los créditos se usan tanto en la web como en la API.
- Recarga créditos desde el panel: sección **"Comprar Créditos"**.

---

## Contacto y soporte

| Canal | Enlace |
|-------|--------|
| Instagram | [@_crissisa](https://www.instagram.com/_crissisa) |
| Telegram | [@crissisa](https://t.me/crissisa) |
| TikTok | [@_crissisa](https://www.tiktok.com/@_crissisa) |
| GitHub | [crissisaa](https://github.com/crissisaa) |

---

<div align="center">

**Hecho por [crissisaa](https://github.com/crissisaa) — Tatadata**

</div>
