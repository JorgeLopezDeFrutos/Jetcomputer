# JetComputer · Dashboard de valoración ITAD

Aplicación estática de valoración orientativa a partir de un archivo Excel. **No incluye autenticación de empleados ni precios de mercado verificados.** No se debe utilizar como sistema corporativo privado simplemente por publicarlo en GitHub Pages.

## Funcionamiento
1. Abrir `jetcomputer_dashboard.html` en un navegador compatible.
2. Seleccionar un Excel/CSV (máximo 10 MB).
3. Pulsar **Analizar Excel** y revisar los resultados; la valoración procede de reglas locales, no de cotizaciones web verificadas.
4. Descargar CSV. Los datos del Excel se procesan en el navegador, no se envían a una API del proyecto.

**Dependencia externa:** SheetJS 0.18.5 se carga desde jsDelivr. La carga del código JavaScript sí requiere una solicitud al CDN. Para un entorno corporativo se recomienda servir una copia revisada y fijada de la librería desde un dominio controlado, con políticas CSP adecuadas.

## Publicación
El workflow `.github/workflows/jekyll-gh-pages.yml` publica archivos estáticos con GitHub Actions:
- `contents: read`: lectura del repositorio.
- `pages: write`: permiso de publicación.
- `id-token: write`: autorización OIDC del despliegue.
Estos permisos **no autentican a los usuarios del dashboard**.
En GitHub: Settings > Pages > Build and deployment > GitHub Actions. Revisar ejecución desde Actions.

> **Importante:** si GitHub Pages es público, cualquier visitante podrá abrir la aplicación. El repositorio también es público. No publicar datos confidenciales en el repositorio. No añadir contraseñas, client secrets, API keys ni tokens al HTML, al JavaScript ni a los archivos de Git.

## Controles aplicados
- Escape HTML en referencias importadas antes de mostrarlas en la tabla.
- Mitigación de fórmulas inyectadas en CSV al abrirlo con programas de hoja de cálculo.
- Rechazo de archivos de más de 10 MB y mensaje ante errores de lectura.
- Reparación del YAML de despliegue, que tenía HTML incorporado por error.

Estas medidas son defensas parciales, **no convierten la aplicación en un portal seguro**. Conviene probar con archivos de auditoría reales, revisar la superficie de ataque del parser Excel y la actualización de dependencias.

## Arquitectura corporativa recomendada (pendiente de implementación)
Para acceso desde la empresa y fuera de ella:
1. Frontend servido por HTTPS desde un dominio controlado.
2. Backend/API que valide cada petición, registre eventos y autorice operaciones.
3. Microsoft Entra ID (OpenID Connect, Authorization Code + PKCE) con MFA y grupos/roles.
4. Sesión gestionada por backend mediante cookie `Secure`, `HttpOnly`, `SameSite` y protección CSRF cuando corresponda; no persistir tokens en `localStorage`.
5. Los secretos y credenciales de servicio en almacenamiento protegido del servidor; nunca en frontend ni Git.
6. Filtro de acceso en el servidor, limitación de solicitudes, copias de seguridad y pruebas de recuperación.

**No confundir ocultar un enlace o añadir un formulario HTML con control de acceso real.** El backend y Entra ID requieren infraestructura, configuración de tenant, registro de aplicación, dominios y permisos, que no están presentes en este repositorio.

## Aviso
Las valoraciones actuales son estimaciones calculadas por reglas locales. Validar manualmente antes de usarlas en compras o ventas.
