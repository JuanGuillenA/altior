# Contexto inicial del proyecto Altiir

Resumen de la conversación de planificación entre Juan y Claude (chat), el 7 de noviembre de 2026.
Sirve para que Claude Code empiece con el mismo contexto. Las decisiones técnicas y las reglas
están en `CLAUDE.md`; este documento guarda el resto: notas del abogado, comparación con la
competencia, riesgos, estimaciones y plan.

> Las notas del abogado son apuntes tomados de una conversación: **no están validadas**.
> Lo ambiguo está marcado como POR DEFINIR y se resuelve con él después de la auditoría.

## 1. Notas del abogado por módulo

Nombre del producto: Altiir (el repositorio se llama `altior`).

| Módulo | Lo que pidió |
|---|---|
| Inicio | Calendario como el del AVAC; que los correos se abran en el inicio; tareas del día |
| Clientes | Que la ficha se abra en el mismo lugar, no en una pestaña nueva |
| Expedientes | Elegir los casos; buscar por tres criterios (cuáles: POR DEFINIR) |
| Audiencias | Conectadas con las personas involucradas en todo momento; registro de actividad |
| Biblioteca jurídica | Buscar un artículo dentro de un libro subido; seleccionar un texto y buscarlo por artículo |
| Calculadora de plazos y términos | Conectada con el cliente, las tareas y el resto del sistema |
| SATJE | Conectar con la página de la Función Judicial para no abrirla en otro lugar |
| Integraciones internas | SRI, firma electrónica, conectores de correo |
| Correo | Poder usar el correo dentro de la app |
| WhatsApp | Mensaje automático al cliente sobre lo que está haciendo el abogado, para que no se quede sin información |
| Honorarios y pagos | Mejor diseño que el actual |
| Fondos y terceros | Junto con honorarios y pagos; sirve para pagar valores de trámites que son distintos de los honorarios |
| Contabilidad de la firma | Solo el administrador tiene acceso completo |
| Reportes | "Lo mismo que documentos" (ambiguo: ¿mismos permisos?, POR DEFINIR) |
| Licencia y suscripción | Enlazar la facturación de la tarjeta con el correo y los mensajes |

Otros puntos: el diseño actual del prototipo es feo y debe ser claro y útil; la suscripción es mensual
(precio por definir); no habrá rol de cliente.

## 2. Competencia

- **Lexis Ecuador:** sobre todo biblioteca de investigación jurídica (legislación, jurisprudencia,
  doctrina, versiones históricas de las leyes). Su gestión de causas es secundaria. No hace
  facturación, contabilidad del despacho ni fondos de terceros.
- **MyCase:** gestión de despacho para EE. UU. y Canadá (casos, clientes, facturación, contabilidad
  fiduciaria, pagos en línea, documentos, tiempo). Sin contenido ni reglas de Ecuador.
- **Diferenciador de Altiir:** gestión del despacho + biblioteca + integraciones locales (SATJE, SRI,
  firma electrónica, WhatsApp) + plazos y términos ecuatorianos, en un solo producto.

## 3. Riesgos y dudas técnicas conocidas

- **SATJE:** no se conoce una API pública oficial. Una integración podría ser frágil. Además, la
  página probablemente no permite mostrarse dentro de otra web. Verificar antes de prometerla.
- **SRI:** los servicios web de comprobantes electrónicos probablemente no tienen costo; sí lo tiene
  el certificado de firma electrónica. Verificar con la documentación oficial.
- **Firma electrónica:** cada usuario usa su propio certificado (.p12). Decidir dónde se firma
  (equipo del usuario o servidor) por seguridad.
- **Pagos de la suscripción:** Payphone o Datafast; verificar soporte de cobros recurrentes y comisiones.
- **WhatsApp:** API oficial de empresa; cobra por conversación. Afecta el margen.
- **Calculadora de plazos:** un error puede hacer perder un caso. Requiere reglas validadas por el
  abogado, pruebas automáticas y un aviso de responsabilidad.
- **Datos personales:** ley de protección de datos de Ecuador y secreto profesional.
- **Servidor:** es de un conocido de Juan. Los archivos se cifran con una clave que Juan controla,
  fuera de ese servidor.
- **Precio:** con comisiones, hosting, correo y WhatsApp el margen puede ser estrecho. Calcular antes de fijarlo.

## 4. Estimación (un desarrollador a tiempo completo, si el código legado sirve de referencia)

- Versión mínima vendible: unos 3 a 4 meses.
- Producto completo: unos 9 a 11 meses.
- Las integraciones (SATJE, SRI, firma, WhatsApp) son las más inciertas.

## 5. Plan de trabajo propuesto

1. **Auditoría** del código en `legacy-python/` → `docs/auditoria/` (módulos, tablas, reglas,
   integraciones, partes a medias, problemas de seguridad).
2. **Esqueleto del proyecto:** Spring Boot, React con TypeScript, `docker-compose.yml` con PostgreSQL y
   MinIO, Flyway, workflow de GitHub Actions, `.env.example`.
3. **Base:** autenticación con 2FA, roles, separación entre despachos, registro de auditoría, pruebas.
4. **Documento de fichas por módulo**, prellenado con la auditoría, para revisar con el abogado
   (¿se queda, cambia o se elimina?).
5. **Alcance de la primera versión** y fechas, con sus respuestas.
6. **Módulos de uno en uno:** ficha → plan → pruebas → código → revisión de Juan → commit.

## 6. Preguntas pendientes para el abogado

- Precio, por usuario o por despacho, y prueba gratuita.
- Alcance de la primera versión.
- Reglas de plazos y términos por tipo de proceso, feriados y quién las valida.
- Quién da de alta a los usuarios del despacho y qué ve cada rol (en especial el asistente).
- Los tres criterios de búsqueda de expedientes.
- Origen del contenido de la biblioteca (libros subidos por el usuario, leyes públicas o ambos).
- Textos legales: términos de servicio, privacidad, responsabilidad por la calculadora.
- Acuerdo entre Juan y el abogado: propiedad del código, soporte y responsabilidades.
