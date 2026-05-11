# reglas_bd.md

# Generador de Tablas Db2 for i (IBM i)

## Objetivo

Generar scripts SQL para IBM i usando Db2 for i respetando exactamente el formato corporativo definido.

El agente debe solicitar únicamente la información técnica mínima necesaria para construir la tabla.

La información documental adicional debe generarse automáticamente o dejarse en blanco.

---

# Información obligatoria que debe solicitar el agente

## Datos de tabla

El agente debe solicitar:

1. Nombre lógico de la tabla
2. Nombre físico IBM i

---

## Campos

Por cada campo el agente debe solicitar:

1. Nombre largo
2. Nombre corto para `FOR COLUMN`
3. Tipo de dato
4. Longitud
5. ¿Permite NULL?

---

## Llave primaria

El agente debe solicitar:

- Campo o campos de llave primaria

---

# Reglas para tipos de dato

## Tipo alfa

Si el usuario indica:

```text
alfa
```

El agente debe generar:

```sql
CHAR(longitud)
```

---

# Reglas para metadata documental

El agente NO debe solicitar información documental adicional.

NO debe preguntar por:

- Objetivo
- Uso de datos
- Proyecto
- Labels
- Text
- Comments
- Historial de cambios

Sin embargo, SIEMPRE debe generar todas esas secciones.

---

# Reglas de inferencia

El agente puede inferir información razonablemente.

Ejemplos:

## Nombre tabla

Si la tabla es:

```text
PROVINCIAS
```

Puede generar:

```text
TABLA CATALOGO DE PROVINCIAS
```

## Objetivo

Puede generar:

```text
ALMACENAR EL CATALOGO DE PROVINCIAS
```

## Uso de datos

Puede generar:

```text
TABLA DE CATALOGOS
```

## Fecha

Si el usuario no la indica, usar la fecha actual.

---

# Reglas cuando no exista información

Cuando el agente no tenga información suficiente:

- Debe dejar el valor en blanco
- Debe mantener todas las secciones
- NO debe eliminar bloques

Ejemplo:

```sql
LABEL ON TABLE PROVINCIAS IS
    '';

COMMENT ON TABLE PROVINCIAS IS
    '';

LABEL ON COLUMN PROVINCIAS (
    CODPRO IS '',
    DESPRO IS ''
);
```

---

# Reglas estrictas de formato

El SQL debe respetar exactamente este formato.

## Formato correcto

```sql
CODIGO_PROVINCIA
    FOR COLUMN CODPRO
    CHAR(5)
    NOT NULL
```

## Formato incorrecto

```sql
CODPRO CHAR(5) FOR COLUMN "CODPRO"
```

---

# Reglas obligatorias

- No usar columnas en una sola línea
- No usar comillas dobles
- No usar `DEFAULT` salvo que el usuario lo solicite
- Mantener el formato FOR COLUMN exactamente igual
- Mantener todos los bloques corporativos
- Mantener el encabezado corporativo
- Los `LABEL ON COLUMN` deben generarse en bloque
- Los `COMMENT ON COLUMN` deben generarse en bloque

---

# Plantilla obligatoria

```sql
--====================================================================
-- NOMBRE DE LA TABLA: <NOMBRE_FISICO>                               =
-- DESCRIPCIÓN:                                                      =
-- OBJETIVO:                                                         =
--                                                                   =
-- USO DE DATOS:                                                     =
--                                                                   =
-- PROYECTO:                                                         =
--                                                                   =
--====================================================================
-- HECHO POR:                                                        =
--                                                                   =
-- FECHA:                                                            =
--                                                                   =
--====================================================================
-- HISTORIAL DE CAMBIOS:                                             =
--====================================================================
-- HECHO POR:                                                        =
--                                                                   =
-- FECHA:                                                            =
--                                                                   =
-- DESCRIPCIÓN CAMBIO:                                               =
--                                                                   =
--====================================================================

CREATE TABLE <NOMBRE_LOGICO> (
    <NOMBRE_LARGO_CAMPO_1>
        FOR COLUMN <NOMBRE_CORTO_CAMPO_1>
        <TIPO_DATO>
        NOT NULL,

    <NOMBRE_LARGO_CAMPO_2>
        FOR COLUMN <NOMBRE_CORTO_CAMPO_2>
        <TIPO_DATO>
        NOT NULL,

    CONSTRAINT <NOMBRE_FISICO>_PK
        PRIMARY KEY (<CAMPO_PK>)
)
RCDFMT REG+<NOMBRE_LOGICO>; --Acortar a 10 posiciones


LABEL ON TABLE <NOMBRE_LOGICO> IS
    '<DESCRIPCION_TABLA>';

COMMENT ON TABLE <NOMBRE_LOGICO> IS
    '<DESCRIPCION_TABLA>';

LABEL ON COLUMN <NOMBRE_LOGICO> (
    <CAMPO_CORTO_1> IS '<LABEL_CAMPO_1>',
    <CAMPO_CORTO_2> IS '<LABEL_CAMPO_2>'
);

LABEL ON COLUMN <NOMBRE_LOGICO> (
    <CAMPO_CORTO_1> TEXT IS '<TEXT_CAMPO_1>',
    <CAMPO_CORTO_2> TEXT IS '<TEXT_CAMPO_2>'
);

COMMENT ON COLUMN <NOMBRE_LOGICO> (
    <CAMPO_CORTO_1> IS '<COMMENT_CAMPO_1>',
    <CAMPO_CORTO_2> IS '<COMMENT_CAMPO_2>'
);
```

---

# Otras reglas
  - utiliza vscode_askQuestions para solicitar la información de forma estructurada.

# Fin del documento
