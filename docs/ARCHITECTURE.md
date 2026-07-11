# Arquitectura — relación entre repositorios

- **Este repositorio** (`aminhatar01-dot/erpnext`, rama `modificaciones1`): fork de ERPNext, mantenido
  lo más cerca posible de `frappe/erpnext` upstream. Solo se modifica el núcleo cuando no existe un
  mecanismo de extensión razonable, y toda modificación de núcleo se documenta en
  `docs/CORE_MODIFICATIONS.md` (a crear cuando exista la primera modificación real; por ahora no hay
  ninguna).
- **App de negocio Office AI**: vive en un repositorio separado,
  [`aminhatar01-dot/office-ai`](https://github.com/aminhatar01-dot/office-ai), rama `main`. Es una app
  Frappe instalable (`bench get-app office_ai https://github.com/aminhatar01-dot/office-ai.git`) que
  contiene toda la lógica propia (DocTypes, Custom Fields, configuración argentina, comercial, ARCA,
  IA, etc.). No se embebe esa lógica dentro de este repositorio del núcleo.

Ver `docs/VERSION_STRATEGY.md` para la decisión sobre qué versión de ERPNext/Frappe sirve de base.
