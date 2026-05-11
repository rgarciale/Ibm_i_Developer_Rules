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

# Tipo de fuente

El código generado debe iniciar siempre con:

```rpgle
**FREE
```

---

# Tipo de objeto esperado

Para mantenimientos de tablas generar módulos SQLRPGLE.

Usar:

```rpgle
CTL-OPT NOMAIN;
```

---

# Reglas FULL FREE

Usar siempre:

```rpgle
DCL-PROC
DCL-PI
DCL-S
DCL-DS
EXEC SQL
END-PROC
```

No usar:

```rpgle
C
D
F
```

---

# Arquitectura esperada

El agente debe generar código desacoplado.

Separar:

1. Validaciones
2. Acceso a datos
3. CRUD
4. Manejo de errores
5. Reglas de negocio

---

# Reglas CRUD

Generar procedimientos independientes:

- Inserta
- Actualiza
- Elimina
- Existe
- Obtiene

Cada procedimiento debe tener una única responsabilidad.

---

# Reglas SQL

## INSERT

Validar existencia antes de insertar.

No mezclar validaciones complejas dentro del INSERT.

---

## UPDATE

Actualizar únicamente por llave primaria.

---

## DELETE

Eliminar únicamente por llave primaria.

---

## EXISTS

Usar COUNT(1).

Debe retornar indicador.

---

# Reglas SQLCODE

Validar SQLCODE después de cada operación SQL.

Centralizar manejo de mensajes de error cuando sea posible.

---

# Reglas NOMAIN

Cuando el módulo use:

```rpgle
CTL-OPT NOMAIN;
```

No usar:

```rpgle
*INLR = *ON;
```

Toda la lógica debe estar encapsulada en procedimientos.

---

# Reglas de reutilización

Las validaciones deben poder reutilizarse.

Ejemplos:

```text
ExisteProvincia
ValidaProvincia
ObtieneProvincia
```

No duplicar lógica.

---

# Reglas de procedimientos

Los procedimientos deben ser:

- pequeños
- reutilizables
- legibles
- especializados

Evitar procedimientos monolíticos.

---

# Reglas de comentarios

Cada procedimiento debe iniciar con bloque descriptivo.

Formato:

```rpgle
////////////////////////////////////////////////////////////////////////
// Nombre del Procedimiento:                                          //
// Descripción.............:                                          //
// Parámetros de Entrada...:                                          //
// Valor de Retorno........:                                          //
////////////////////////////////////////////////////////////////////////
```

---

# Reglas para SQL embebido

Usar siempre:

```rpgle
EXEC SQL
```

No usar APIs antiguas.

No usar SQL dinámico salvo que el usuario lo solicite.

---

# Reglas para tablas Db2 for i

El agente debe respetar exactamente la definición SQL de la tabla.

Si existe:

```sql
CODIGO_PROVINCIA
    FOR COLUMN COD_PROV
```

Debe utilizar correctamente los nombres SQL.

No inventar columnas.

---

# Reglas importantes

- No usar RPG columnar
- No usar fixed format
- No usar lógica duplicada
- No generar procedimientos gigantes
- No mezclar lógica SQL con validaciones complejas
- No dejar SQL sin validar
- No generar código acoplado

---

# Reglas para ejemplo PROVINCIAS

Tabla:

```text
PROVINCIAS
```

Campos:

```text
CODIGO_PROVINCIA
NOMBRE_PROVINCIA
DESCRPCION
```

Llave primaria:

```text
CODIGO_PROVINCIA
```

El mantenimiento debe permitir:

- Insertar provincia
- Actualizar provincia
- Eliminar provincia
- Validar existencia
- Consultar provincia

Aplicando principios SOLID.

---

# Fin del documento
