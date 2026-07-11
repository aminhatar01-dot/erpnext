# Estrategia de versión — Office AI sobre ERPNext

## Hallazgo

La rama por defecto del fork (`develop`, de la cual `modificaciones1` parte sin commits propios) está en
**`17.0.0-dev`**: es el tronco de desarrollo continuo de ERPNext/Frappe hacia la próxima versión mayor,
**no una versión de release**. Requiere Python ≥3.14, una versión muy reciente y sin recorrido de
producción probado en el ecosistema Frappe todavía.

La última versión **estable y soportada oficialmente** es la serie **v16** (tag más reciente: `v16.26.2`).
`develop` y `v16.26.2` no son la misma línea: 4138 commits de diferencia en un sentido, 2315 en el otro.
No se puede "avanzar" de v16 a develop con un simple fast-forward ni viceversa.

## Riesgo de construir la primera versión comercial sobre `develop` (v17-dev)

- API interna y hooks pueden cambiar sin aviso antes del release estable de v17.
- Sin garantías de soporte, sin changelog de breaking changes consolidado todavía.
- Requiere Python 3.14, incrementando la superficie de incompatibilidad con infraestructura de terceros
  (paquetes del sistema, imágenes base, hosting) que hoy en día mayormente soporta 3.11/3.12.
- Ningún despliegue productivo de referencia conocido corre sobre `17.0.0-dev`.

**Conclusión: no se debe declarar "lista para producción" ninguna instalación de Office AI construida
sobre `develop`/`17.0.0-dev` en su estado actual.** Esto queda bloqueado explícitamente hasta que se
tome una decisión informada (ver más abajo) o hasta que Frappe/ERPNext publique v17 como estable.

## Restricciones que condicionan la decisión

- No se debe destruir ni reemplazar `modificaciones1`.
- No se debe hacer force-push ni reescribir historia compartida.
- `modificaciones1` hoy apunta exactamente al mismo commit que `develop` (0 commits propios), por lo
  que **no hay trabajo que preservar todavía** — es el mejor momento posible para fijar la base correcta,
  antes de acumular commits sobre la rama equivocada.
- El pedido explícito del usuario indica que el código fuente a modificar y el destino de los cambios
  es este mismo repositorio (`aminhatar01-dot/erpnext`), rama `modificaciones1`.

## Decisión adoptada

1. **`modificaciones1` continúa existiendo y recibiendo los commits de Office AI**, tal como fue
   solicitado explícitamente. No se destruye ni se reemplaza.
2. **Se documenta y deja bloqueado**: ninguna instalación construida sobre el estado actual de
   `modificaciones1` (`17.0.0-dev`) puede declararse lista para producción comercial, hasta que se
   cumpla una de estas dos condiciones:
   - (a) Frappe/ERPNext publican v17 como estable y se re-audita esta decisión, o
   - (b) se decide explícitamente re-basar `modificaciones1` sobre la serie estable `v16.26.2`
     (acción que requeriría reescribir la rama remota — force push — y por lo tanto **requiere
     autorización explícita del usuario**, ya que está en la lista de acciones prohibidas por defecto).
3. **La app `office_ai`** (código propio, ver `docs/ARCHITECTURE.md`) se desarrolla como aplicación
   Frappe independiente, escribiendo el código de forma compatible con las APIs estables de Frappe/ERPNext
   (evitando deliberadamente APIs marcadas como experimentales o exclusivas de `develop`), de modo que
   migrar la base de v17-dev a v16 estable en el futuro implique el menor costo posible.
4. **Para pruebas y validación reales** (Docker, CI, smoke tests) se usará el propio `develop` del fork
   por ser la rama activa, dejando registrado en cada reporte de pruebas (`docs/TEST_REPORT.md`) que
   corren sobre una base pre-release, no sobre la versión estable.
5. Cuando llegue el momento de ofrecer una instalación a un cliente real, el checklist de
   `docs/PRODUCTION_CHECKLIST.md` exigirá una re-confirmación explícita de esta decisión de versión
   antes de permitir marcar el proyecto como "listo para producción".

## Acción pendiente que requiere decisión del usuario

- **¿Autorizás re-basar `modificaciones1` sobre el tag estable `v16.26.2`?** Esto implicaría un
  force-push sobre `origin/modificaciones1` (dado que hoy no hay commits propios que perder, el riesgo
  de pérdida de trabajo es nulo, pero la acción en sí requiere confirmación explícita por política).
  Hasta recibir esa confirmación, se continúa sobre `develop` documentando el riesgo como se indica arriba.
