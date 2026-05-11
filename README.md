# IBM i Developer Rules

Repositorio para centralizar y versionar archivos de reglas y lineamientos de desarrollo en IBM i.

## Objetivo

Este repositorio servira como fuente unica para:

- Estandares de codificacion (RPGLE, CLLE, SQL, DDS, etc.).
- Convenciones de nombres para objetos y artefactos.
- Reglas de calidad, revisiones y despliegue.
- Buenas practicas de seguridad y rendimiento.

## Estructura sugerida

```text
rules/
	coding/
		rpgle.md
		clle.md
		sql.md
	naming/
		objects.md
		libraries.md
	quality/
		code-review.md
		testing.md
	deployment/
		release-checklist.md
```

## Convenciones recomendadas

- Un archivo por tema para facilitar mantenimiento.
- Titulos claros y secciones consistentes en todos los documentos.
- Ejemplos practicos para cada regla importante.
- Historial de cambios en los commits, con mensajes descriptivos.

## Proximos pasos

1. Crear la carpeta `rules/` con la estructura base.
2. Agregar las primeras reglas de codificacion para RPGLE y SQL.
3. Definir checklist de revision de codigo y despliegue.