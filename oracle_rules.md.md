# Reglas de Oráculo — Semana 4 (ts-api-rest)

**Objeto de prueba:** `POST /api/v1/juegos`

Estas reglas definen criterios **pass/fail** para evaluar casos sistemáticos sobre el endpoint seleccionado.  
Se distinguen **reglas mínimas** (seguras, poco asumidas) y **reglas estrictas** (cuando aplique).

---

## Reglas mínimas (aplican a todos los casos)

- **OR1 (Registro):**  
  Cada ejecución debe registrar el `http_code` y guardar el cuerpo de la respuesta como evidencia (JSON o texto).

- **OR2 (No HTML):**  
  La respuesta no debe ser HTML (si el primer carácter no vacío es `<`, se considera fallo del oráculo).

- **OR3 (No 5xx):**  
  La respuesta no debe retornar códigos `5xx`.  
  (`5xx` implica fallo del servicio ante la solicitud).

- **OR4 (Content-Type esperado):**  
  Si existe cuerpo de respuesta, el encabezado `Content-Type` debe incluir `application/json`.

---

## Reglas por partición (EQ) y valores límite (BV)

Las particiones se definen a partir de la validez del **payload JSON** enviado al endpoint.

- **OR5 (Payload vacío o ausente):**  
  Si el cuerpo de la solicitud está vacío o ausente, entonces `http_code ∈ {400, 422}` y **nunca** `200` o `201`.

- **OR6 (Campos obligatorios ausentes):**  
  Si falta al menos un campo obligatorio del recurso *juego*, entonces `http_code ∈ {400, 422}`.

- **OR7 (Tipos de datos inválidos):**  
  Si un campo con tipo esperado numérico o fecha se envía con un tipo incorrecto (por ejemplo texto), entonces `http_code ∈ {400, 422}`.

- **OR8 (Límites de cadenas — BV):**  
  Si un campo string obligatorio tiene longitud menor al mínimo permitido o contiene solo espacios en blanco, entonces `http_code ∈ {400, 422}`.

- **OR9 (Caso válido mínimo):**  
  Si el payload cumple con todos los campos obligatorios y tipos esperados, entonces `http_code ∈ {200, 201}` y **nunca** `4xx` o `5xx`.

---

## Reglas estrictas (opcional / reportar como “estrictas”)

- **OR10 (Consistencia semántica cuando hay éxito):**  
  Si `http_code ∈ {200, 201}`, el cuerpo de la respuesta debería incluir un identificador del recurso creado (por ejemplo `id` o `_id`) no vacío.

- **OR11 (No persistencia de datos inválidos):**  
  Si el caso fue inválido (`http_code ∈ {400, 422}`), entonces una consulta posterior de lectura no debe devolver el recurso.

> **Nota:**  
> OR10 y OR11 se reportan como chequeos estrictos porque dependen del estado interno del SUT, de la persistencia y de la implementación concreta del backend.