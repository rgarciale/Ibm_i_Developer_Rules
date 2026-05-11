# reglas_bd.md

# Generador de Tablas Db2 for i (IBM i)

## Objetivo

Este documento define las reglas para generar scripts SQL de creación de tablas en IBM i usando Db2 for i, siguiendo el formato estándar corporativo.

El agente debe crear scripts SQL completos a partir de datos básicos suministrados por el usuario.

---

# Flujo obligatorio del agente

El agente debe recopilar la información en este orden:

1. Metadata de la tabla
2. Definición de campos
3. Constraints
4. Etiquetas y comentarios
5. Generación final del SQL

Si falta información obligatoria, el agente NO debe generar el SQL todavía. Debe preguntar únicamente por la sección faltante.

---

# 1. Metadata de la tabla

El agente debe solicitar:

- Nombre lógico de la tabla
- Nombre físico/sistema de la tabla
- Descripción de la tabla
- Objetivo de la tabla
- Uso de datos
- Proyecto
- Persona que la crea
- Fecha de creación

## Reglas

- Si no se indica nombre físico, debe proponer uno compatible con IBM i.
- El nombre físico debe ser de máximo 10 caracteres.
- El nombre físico debe ser único.
- El nombre lógico puede ser largo y descriptivo.

## Ejemplo

```text
Tabla lógica: PROVINCIAS
Tabla sistema: REGPROV
Descripción: TABLA CATALOGO DE PROVINCIAS
Objetivo: ALMACENAR EL CATALOGO DE PROVINCIAS
Uso de datos: TABLA DE CATALOGOS
Proyecto: CATALOGOS BASE
Creado por: RAMIRO GARCIA
Fecha: AGOSTO DE 2022
```

---

# 2. Definición de campos

Por cada campo, el agente debe solicitar:

- Nombre largo del campo
- Nombre corto para `FOR COLUMN`
- Tipo de dato
- Longitud
- Si permite NULL
- Valor por defecto, si aplica
- Descripción corta para LABEL
- Descripción larga para TEXT y COMMENT

## Reglas

- El nombre corto debe ser compatible con IBM i.
- Preferir nombres cortos de máximo 10 caracteres.
- Si el campo es obligatorio, usar `NOT NULL`.
- Si permite nulos, no agregar `NOT NULL`.
- Usar `FOR COLUMN` en todos los campos.
- No inventar campos que el usuario no haya indicado.

---

# 3. Constraints

El agente debe preguntar por:

- Llave primaria
- Llaves únicas
- Llaves foráneas
- Checks
- Valores por defecto
- Índices adicionales

---

# 4. Formato obligatorio del SQL

El agente debe generar el SQL con la estructura corporativa estándar IBM i Db2.

---

# 5. Reglas de generación

- Usar siempre `CREATE TABLE`
- Usar siempre `FOR COLUMN`
- Usar siempre `RCDFMT`
- Usar siempre `RENAME TABLE ... FOR SYSTEM NAME`
- Usar siempre:
  - `LABEL ON TABLE`
  - `COMMENT ON TABLE`
  - `LABEL ON COLUMN`
  - `LABEL ON COLUMN ... TEXT`
  - `COMMENT ON COLUMN`

---

# Fin del documento
