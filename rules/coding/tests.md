# reglas_test_sqlrpgle.md

# Generador de Programas de Prueba SQLRPGLE Full Free

## Objetivo

Generar programas de prueba SQLRPGLE full free para IBM i, orientados a validar programas CRUD que reciben una estructura de datos, una operación y retornan un código de resultado.

El test debe ejecutar operaciones como INSERT, UPDATE y DELETE, evaluar el resultado devuelto por el programa probado y mostrar un resumen final de pruebas ejecutadas.

---

# Formato base obligatorio

El código generado debe iniciar siempre con:

```rpgle
**FREE

CTL-OPT DFTACTGRP(*NO) OPTION(*SRCSTMT:*NODEBUGIO);
```

---

# Tipo de programa

Generar un programa SQLRPGLE ejecutable de prueba.

No usar:

```rpgle
CTL-OPT NOMAIN;
```

salvo que el usuario lo solicite explícitamente.

---

# Estructura obligatoria del código

El programa de prueba debe generarse en este orden:

1. `**FREE`
2. `CTL-OPT DFTACTGRP(*NO) OPTION(*SRCSTMT:*NODEBUGIO);`
3. Estructura de entrada requerida por el programa CRUD
4. Estructura para registrar el resultado de la prueba
5. Prototipo del programa externo a probar
6. Variables de trabajo
7. Configuración SQL inicial
8. Ejecución secuencial de las pruebas
9. Resumen final
10. Limpieza de datos de prueba
11. `*INLR = *ON`
12. Procedimientos internos de inicialización, ejecución, evaluación, despliegue y limpieza

---

# Estructura de entrada

El test debe declarar una estructura de datos compatible con el programa probado.

Ejemplo:

```rpgle
// Estructura de entrada requerida por CRUD_PROV.
DCL-DS ProvinciaData QUALIFIED;
  CodigoProvincia CHAR(3);
  NombreProvincia CHAR(15);
  Descripcion     CHAR(30);
END-DS;
```

Reglas:

- Debe usar `DCL-DS`.
- Debe usar `QUALIFIED`.
- Los campos deben coincidir con los esperados por el programa CRUD.
- La estructura debe declararse antes del `DCL-PR`.

---

# Estructura de resultado de prueba

El test debe declarar una estructura para controlar el resultado de cada prueba.

Formato esperado:

```rpgle
// Estructura para registrar el resultado de la prueba.
DCL-DS ResultadoPrueba QUALIFIED;
  NumeroPrueba INT(5);
  Descripcion  CHAR(50);
  Resultado    CHAR(10);
  SqlCode      INT(10);
  Mensaje      CHAR(50);
END-DS;
```

Reglas:

- `Resultado` debe contener `PASS` o `FAIL`.
- `SqlCode` debe almacenar el código devuelto por el programa probado.
- `Mensaje` debe describir el resultado de la prueba.

---

# Prototipo del programa probado

El programa de prueba debe llamar al programa CRUD mediante `DCL-PR EXTPGM`.

Ejemplo:

```rpgle
// Prototipo del programa CRUD_PROV.
DCL-PR CRUD_PROV EXTPGM('CRUD_PROV');
  inProv LIKEDS(ProvinciaData) CONST;
  OperacionParm CHAR(10) CONST;
  outResultado ZONED(10:0);
END-PR;
```

Reglas:

- Usar `DCL-PR` únicamente para el programa externo que se va a probar.
- El prototipo debe usar `EXTPGM`.
- El parámetro de entrada debe usar `LIKEDS`.
- La estructura usada por `LIKEDS` debe estar definida previamente con `DCL-DS`.
- El resultado debe recibirse en un parámetro de salida.

---

# Variables de trabajo

El test debe declarar variables de trabajo similares a:

```rpgle
// Variables de trabajo.
DCL-DS pruebaInsert LIKEDS(ProvinciaData);
DCL-S codigoResultado ZONED(10:0) INZ(0);
DCL-S totalPruebas INT(5) INZ(0);
DCL-S pruebasExitosas INT(5) INZ(0);
DCL-DS resultadoTest LIKEDS(ResultadoPrueba);
```

Reglas:

- Usar una estructura de datos para preparar los datos de prueba.
- Usar una variable para capturar el resultado devuelto por el CRUD.
- Llevar contador de total de pruebas.
- Llevar contador de pruebas exitosas.
- Usar una estructura para almacenar el resultado actual.

---

# Configuración SQL

Antes de ejecutar las pruebas, incluir:

```rpgle
EXEC SQL SET OPTION COMMIT = *NONE;
```

---

# Flujo principal de ejecución

El programa debe ejecutar las pruebas de forma secuencial.

Formato esperado:

```rpgle
EXEC SQL SET OPTION COMMIT = *NONE;

InicializaPruebaInsert();
EjecutaPruebaInsert();
EvaluaResultadoInsert();
MuestraResultadoInsert();

InicializaPruebaUpdate();
EjecutaPruebaUpdate();
EvaluaResultadoUpdate();
MuestraResultadoUpdate();

InicializaPruebaDelete();
EjecutaPruebaDelete();
EvaluaResultadoDelete();
MuestraResultadoDelete();

MuestraResumenPruebas();
LimpiaPruebaInsert();

*INLR = *ON;
```

Reglas:

- Cada operación debe tener procedimientos separados.
- No mezclar inicialización, ejecución, evaluación y despliegue en un solo procedimiento.
- Ejecutar limpieza al final para no dejar registros residuales.
- Finalizar con `*INLR = *ON`.

---

# Procedimientos requeridos por operación

Para cada operación a probar, generar estos procedimientos:

```text
InicializaPrueba<Operacion>
EjecutaPrueba<Operacion>
EvaluaResultado<Operacion>
MuestraResultado<Operacion>
```

Ejemplo:

```text
InicializaPruebaInsert
EjecutaPruebaInsert
EvaluaResultadoInsert
MuestraResultadoInsert
```

---

# Inicialización de prueba

Cada procedimiento de inicialización debe:

1. Incrementar `totalPruebas`
2. Inicializar `resultadoTest`
3. Cargar los datos de prueba

Ejemplo:

```rpgle
// Inicializa la estructura de control de la prueba.
DCL-PROC InicializaPruebaInsert;
  totalPruebas += 1;

  resultadoTest.NumeroPrueba = 1;
  resultadoTest.Descripcion = 'INSERT de provincia';
  resultadoTest.Resultado = 'FAIL';
  resultadoTest.SqlCode = 0;
  resultadoTest.Mensaje = 'Sin ejecutar';

  pruebaInsert.CodigoProvincia = '001';
  pruebaInsert.NombreProvincia = 'San Jose';
  pruebaInsert.Descripcion = 'Provincia Central';
END-PROC;
```

Reglas:

- La primera inicialización puede poner `totalPruebas = 0` y `pruebasExitosas = 0`.
- Todas las pruebas deben iniciar como `FAIL`.
- El mensaje inicial debe ser `Sin ejecutar`.

---

# Ejecución de prueba

Cada procedimiento de ejecución debe llamar al programa externo con `CALLP`.

Ejemplo:

```rpgle
// Ejecuta el programa CRUD con la operación INSERT.
DCL-PROC EjecutaPruebaInsert;
  CALLP CRUD_PROV(pruebaInsert : 'INSERT' : codigoResultado);
END-PROC;
```

Reglas:

- Usar `CALLP`.
- Enviar estructura de datos, operación y variable de resultado.
- La operación debe enviarse como literal o constante según el programa probado.

---

# Evaluación de resultado

Cada evaluación debe comparar el resultado devuelto contra cero.

Ejemplo:

```rpgle
// Evalua el resultado devuelto por CRUD_PROV para INSERT.
DCL-PROC EvaluaResultadoInsert;
  resultadoTest.SqlCode = codigoResultado;

  IF codigoResultado = 0;
    resultadoTest.Resultado = 'PASS';
    resultadoTest.Mensaje = 'INSERT ejecutado correctamente';
    pruebasExitosas += 1;
  ELSE;
    resultadoTest.Resultado = 'FAIL';
    resultadoTest.Mensaje = 'INSERT retorno error';
  ENDIF;
END-PROC;
```

Reglas:

- Si el código devuelto es `0`, la prueba es `PASS`.
- Si el código devuelto es distinto de `0`, la prueba es `FAIL`.
- Si la prueba es exitosa, incrementar `pruebasExitosas`.
- Guardar siempre el resultado en `resultadoTest.SqlCode`.

---

# Despliegue de resultado

Cada prueba debe mostrar su resultado con `DSPLY`.

Ejemplo:

```rpgle
// Muestra el resultado de la prueba INSERT.
DCL-PROC MuestraResultadoInsert;
  DSPLY 'TEST INSERT PROVINCIAS';
  DSPLY resultadoTest.Resultado;
  DSPLY resultadoTest.Mensaje;
  DSPLY %CHAR(resultadoTest.SqlCode);
END-PROC;
```

Reglas:

- Usar `DSPLY`.
- Mostrar nombre de la prueba.
- Mostrar `PASS` o `FAIL`.
- Mostrar mensaje.
- Mostrar SQLCODE o código de resultado.

---

# Resumen final

El test debe incluir un procedimiento de resumen.

Ejemplo:

```rpgle
// Muestra el resumen total de pruebas ejecutadas.
DCL-PROC MuestraResumenPruebas;
  DSPLY 'RESUMEN PRUEBAS';
  DSPLY 'TOTAL';
  DSPLY %CHAR(totalPruebas);
  DSPLY 'EXITOSAS';
  DSPLY %CHAR(pruebasExitosas);
  DSPLY 'FALLIDAS';
  DSPLY %CHAR(totalPruebas - pruebasExitosas);
END-PROC;
```

Reglas:

- Mostrar total de pruebas.
- Mostrar pruebas exitosas.
- Mostrar pruebas fallidas.

---

# Limpieza de datos de prueba

El test debe incluir un procedimiento de limpieza.

Ejemplo:

```rpgle
// Limpia el dato de prueba para no dejar registros residuales.
DCL-PROC LimpiaPruebaInsert;
  EXEC SQL
    DELETE FROM PROVINCIAS
      WHERE CODIGO_PROVINCIA = :pruebaInsert.CodigoProvincia;
END-PROC;
```

Reglas:

- Eliminar los datos de prueba usados.
- La limpieza debe ejecutarse al final.
- La limpieza no debe incrementar contadores.
- La limpieza no debe marcar `PASS` o `FAIL`.

---

# Estilo de comentarios

Usar comentarios simples y poco invasivos.

Formato correcto:

```rpgle
// Ejecuta el programa CRUD con la operacion INSERT.
```

También se permite separar procedimientos con:

```rpgle
// ---------------------------------------------------------------------
```

No usar encabezados grandes ni bloques invasivos.

---

# Reglas para procedimientos internos

Los procedimientos internos del test deben definirse con `DCL-PROC`.

No generar `DCL-PR` para procedimientos internos.

Usar `DCL-PR` solo para programas externos con `EXTPGM`.

---

# Reglas de generación

- No usar RPG columnar.
- No usar especificaciones C, D o F.

---

# Principios de diseño aplicables

## Single Responsibility

Cada procedimiento debe tener una responsabilidad única:

- inicializar
- ejecutar
- evaluar
- mostrar
- limpiar

## Open/Closed

Para agregar una nueva operación, crear nuevos procedimientos de prueba sin modificar los existentes.

## Interface Segregation

El test debe usar únicamente los parámetros requeridos por el programa probado.

## Dependency Inversion

El test debe llamar al programa externo mediante prototipo `DCL-PR EXTPGM`.

---

# Checklist antes de entregar

Antes de entregar el código, validar:

1. Inicia con `**FREE`.
2. Tiene `CTL-OPT DFTACTGRP(*NO) OPTION(*SRCSTMT:*NODEBUGIO);`.
3. Tiene estructura de entrada compatible con el programa probado.
4. Tiene `DCL-PR EXTPGM` para el programa probado.
5. No tiene `DCL-PR` para procedimientos internos.
6. Cada prueba tiene inicialización, ejecución, evaluación y despliegue.
7. Existe resumen final.
8. Existe limpieza de datos de prueba.
9. Termina con `*INLR = *ON`.
10. No contiene barras invertidas ni caracteres de escape visibles.

---

# Fin del documento
