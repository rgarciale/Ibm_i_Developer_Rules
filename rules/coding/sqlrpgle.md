# reglas_desarrollo_sqlrpgle.md

# Generador de Programas SQLRPGLE Full Free

## Objetivo

Generar programas o módulos SQLRPGLE en formato full free para IBM i usando SQL embebido, siguiendo una estructura estándar corporativa y aplicando principios SOLID.

El código generado debe ser modular, mantenible, reutilizable y desacoplado.

---

# Principios SOLID obligatorios

El agente debe aplicar principios SOLID en todo el código generado.

---

## S - Single Responsibility Principle

Cada procedimiento debe tener una única responsabilidad.

Ejemplos correctos:

- Un procedimiento para insertar
- Un procedimiento para actualizar
- Un procedimiento para validar existencia
- Un procedimiento para consultar
- Un procedimiento para manejo de errores

No mezclar múltiples responsabilidades en un mismo procedimiento.

---

## O - Open/Closed Principle

El código debe ser extensible sin modificar lógica existente.

Las validaciones y reglas deben encapsularse en procedimientos reutilizables.

Evitar lógica hardcodeada.

---

## L - Liskov Substitution Principle

Las estructuras y procedimientos reutilizables deben mantener contratos compatibles.

Las estructuras de retorno deben ser estándar.

---

## I - Interface Segregation Principle

No crear procedimientos gigantes con múltiples funcionalidades opcionales.

Separar responsabilidades en procedimientos pequeños y específicos.

---

## D - Dependency Inversion Principle

El acceso SQL debe estar encapsulado.

La lógica de negocio no debe depender directamente del SQL embebido.

Separar:

- lógica de negocio
- acceso a datos
- validaciones
- mensajes de error

---


# Formato base obligatorio

El código debe iniciar siempre con:

```rpgle
**FREE

CTL-OPT DFTACTGRP(*NO) OPTION(*SRCSTMT:*NODEBUGIO) COMMIT(*NONE) DBGVIEW(*SOURCE);;
```

---

# Tipo de programa

Generar programas SQLRPGLE o RPGLE ejecutables.

No usar:

```rpgle
CTL-OPT NOMAIN;
```

salvo que el usuario lo solicite explícitamente.

---

# Constantes de operación

Las constantes deben declararse usando `CONST`.

Formato correcto:

```rpgle
DCL-C OP_INSERT CONST('INSERT');
DCL-C OP_UPDATE CONST('UPDATE');
DCL-C OP_DELETE CONST('DELETE');
```

No usar:

```rpgle
DCL-C OP_INSERT CHAR(10) VALUE('INSERT');
```

---

# Estructuras de datos

Toda estructura usada con `LIKEDS(...)` debe definirse previamente usando `DCL-DS`.

Formato esperado:

```rpgle
DCL-DS ProvinciaData QUALIFIED;
  CodigoProvincia CHAR(3);
  NombreProvincia CHAR(15);
  Descripcion     CHAR(30);
END-DS;
```

Reglas:

- Definir la estructura antes del `DCL-PI`
- Usar `QUALIFIED`
- No usar `LIKEDS` sin `DCL-DS`

---

# Interfaz del programa

El programa principal debe usar:

```rpgle
DCL-PI *N;
```

Ejemplo:

```rpgle
DCL-PI *N;
  inProv LIKEDS(ProvinciaData) CONST;
  OperacionParm CHAR(10) CONST;
  outResultado ZONED(10:0);
END-PI;
```

---

# Lógica principal

Incluir siempre al iniciar en los programas .sqlrpgle:

```sqlrpgle
EXEC SQL SET OPTION COMMIT = *NONE;
```

La lógica principal debe usar `SELECT`.

Formato esperado:

```rpgle
SELECT OperacionParm;

  WHEN-IS OP_INSERT;
    outResultado = InsertaProvincia(inProv);

  WHEN-IS OP_UPDATE;
    outResultado = ActualizaProvincia(inProv);

  WHEN-IS OP_DELETE;
    outResultado = EliminaProvincia(inProv);

  OTHER;
    outResultado = -1;

ENDSL;
```

Reglas:

- Usar `SELECT`
- Usar `WHEN-IS`
- Usar `OTHER`
- No usar múltiples `IF`

---

# Finalización del programa

Al final de la lógica principal usar:

```rpgle
*INLR = *ON;
```

---

# Procedimientos internos

Los procedimientos internos deben definirse con `DCL-PROC`.

No generar `DCL-PR` para procedimientos internos.

Formato correcto:

```rpgle
DCL-PROC InsertaProvincia;

  DCL-PI *N INT(10);
    inProv LIKEDS(ProvinciaData) CONST;
  END-PI;

  RETURN SQLCODE;

END-PROC;
```

---

# Reglas para DCL-PR

Solo usar `DCL-PR` cuando el procedimiento sea externo y tenga:

```rpgle
EXTPROC
```

o:

```rpgle
EXTPGM
```

---

# Procedimientos esperados

Para mantenimientos simples generar únicamente:

- Inserta
- Actualiza
- Elimina

---

# Reglas SQL

## INSERT

Formato esperado:

```rpgle
EXEC SQL
  INSERT INTO PROVINCIAS
    (CODIGO_PROVINCIA,
     NOMBRE_PROVINCIA,
     DESCRPCION)
  VALUES
    (:inProv.CodigoProvincia,
     :inProv.NombreProvincia,
     :inProv.Descripcion);

RETURN SQLCODE;
```

---

# Reglas de retorno

Cada procedimiento debe retornar:

```rpgle
RETURN SQLCODE;
```

El tipo de retorno debe ser:

```rpgle
INT(10)
```

---

# Principios SOLID

## Single Responsibility

Cada procedimiento debe hacer únicamente una operación:

- insertar
- actualizar
- eliminar

## Open/Closed

Agregar nuevas operaciones mediante nuevos procedimientos.

## Interface Segregation

Recibir únicamente los parámetros necesarios.

## Dependency Inversion

El SQL embebido debe existir únicamente dentro de procedimientos internos.

---

# Reglas importantes

- Código full free
- No usar RPG columnar
- No usar especificaciones C, D o F
- No generar `DCL-PR` internos
- No usar `VALUE` para constantes
- No usar `CHAR(...)` en `DCL-C`
- No usar tabs
- No usar caracteres de escape visibles
- No dejar procedimientos sin `RETURN`

---

# Fin del documento
