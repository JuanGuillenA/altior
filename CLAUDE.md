# Altiir — Software de gestión jurídica (web, Ecuador)

> Los puntos marcados **(POR DEFINIR)** son decisiones pendientes: no los asumas, pregunta.
> Los marcados **(propuesto)** son sugerencias aún no confirmadas por el ingeniero.

## Qué es el proyecto

Aplicación web para despachos de abogados en Ecuador. Reúne en un solo sistema la gestión del
despacho (clientes, expedientes, audiencias, plazos, honorarios, contabilidad), una biblioteca
jurídica y las integraciones locales (SATJE, SRI, firma electrónica, correo, WhatsApp).

- Modelo de negocio: suscripción mensual; el precio y si es por usuario o por despacho están POR DEFINIR.
- Competidores de referencia: Lexis Ecuador (investigación jurídica) y MyCase (gestión de despacho, EE. UU.).
- Diferenciador: todo en uno, adaptado a la práctica jurídica ecuatoriana.
- Cliente/experto jurídico: un abogado que ya hizo un prototipo en Python (ver "Código legado").

## Cómo trabajamos

- **Juan Alberto Guillén es el ingeniero responsable**; Claude actúa como desarrollador.
- Siempre explica el plan antes de modificar código y espera aprobación en cambios que toquen
  autenticación, roles, base de datos, pagos o plazos legales.
- Un módulo a la vez: ficha → plan → pruebas → código → revisión de Juan → commit.
- Commits pequeños y descriptivos, en español, con una sola idea por commit.
- Si una regla jurídica o de negocio no está en `docs/especificacion/`, **pregunta**; no la inventes.
- Si algo es una suposición tuya, dilo explícitamente.
- Al terminar una tarea, actualiza `docs/` y este archivo si hubo decisiones nuevas.

## Stack

| Capa | Tecnología |
|---|---|
| Backend | Java 21, desarrollado en Visual Studio Code, patrón MVC; Spring Boot (propuesto, confirmar) |
| Frontend | React con TypeScript; librería de componentes POR DEFINIR (propuesto: componentes ya hechos, no diseño desde cero) |
| Base de datos | PostgreSQL |
| Migraciones | POR DEFINIR (propuesto: Flyway) |
| Contenedores | Docker y Docker Compose |
| Almacenamiento de archivos | POR DEFINIR (propuesto: compatible con S3, separado de la base de datos) |
| Control de versiones | Git + GitHub |
| CI/CD | POR DEFINIR (propuesto: GitHub Actions) |
| Hosting | Servidor de un conocido de Juan; suficiente para el inicio (especificaciones, acceso y respaldos POR DEFINIR) |

## Estructura del repositorio

```
CLAUDE.md
.claude/rules/        reglas por tema (seguridad, java, react)
.claude/agents/       subagentes (revisor de seguridad, auditor del legado)
.claude/commands/     comandos propios
docs/especificacion/  una ficha por módulo (llenada con el abogado)
docs/auditoria/       resultado del análisis del código en Python
docs/decisiones/      decisiones tomadas y su motivo
legacy-python/        código del abogado: SOLO LECTURA
backend/              Java
frontend/             React
docker-compose.yml
```

## Comandos

POR DEFINIR hasta que exista el código. Cuando existan, documentar aquí:
levantar el entorno, correr pruebas del backend, correr pruebas del frontend, compilar, migrar la base de datos.

## Reglas no negociables

### Seguridad
1. **Separación entre despachos (multi-tenant):** toda tabla con datos de un despacho lleva el
   identificador del despacho, y toda consulta lo filtra. Un usuario nunca puede ver datos de otro despacho.
2. **Ningún endpoint sin autenticación y sin control de rol.** Por defecto, todo está denegado.
3. **Nada de secretos en el código ni en Git:** claves, tokens, certificados (.p12) y contraseñas
   van en variables de entorno. Nunca leer ni editar archivos `.env`.
4. Contraseñas con hash seguro; nunca en texto plano ni en logs.
5. Validar toda entrada en el servidor; consultas parametrizadas; protección contra XSS y CSRF.
6. HTTPS, cierre de sesión por inactividad, bloqueo por intentos fallidos. Verificación en dos pasos (incluida).
7. Los archivos subidos se validan por tipo y tamaño, y se guardan cifrados (detalle POR DEFINIR).
8. Registro de auditoría: quién vio, creó, editó o borró qué y cuándo.
9. Respaldos automáticos y restauración probada.

### Privacidad (Ley Orgánica de Protección de Datos Personales, Ecuador)
- Datos de clientes y expedientes son sensibles y están bajo secreto profesional.
- Minimizar los datos que se piden y se guardan.
- Debe existir exportación completa de los datos de un despacho y su eliminación al cancelar (plazos POR DEFINIR).
- Política de privacidad, consentimiento y términos de servicio: documentos pendientes (POR DEFINIR).
- Los libros que suben los usuarios a la biblioteca son privados de cada usuario o despacho.

### Roles (confirmar con el abogado)
- Superadministrador (Juan / plataforma).
- Administrador del despacho: acceso total, incluida contabilidad y reportes.
- Abogado: expedientes, clientes, audiencias, plazos.
- Asistente: acceso limitado (qué ve y qué no: POR DEFINIR).
- **No existe rol de cliente:** el sistema es solo para abogados y su personal. Los clientes no tienen acceso; reciben mensajes por WhatsApp o correo.

## Módulos (según las notas del abogado)

Inicio (calendario, correos, tareas del día) · Clientes · Expedientes (búsqueda por 3 criterios) ·
Audiencias · Actividad · Biblioteca jurídica (buscar por artículo en libros subidos) ·
Calculadora de plazos y términos (conectada a clientes y tareas) · SATJE ·
Integraciones (SRI, firma electrónica, correo) · WhatsApp automático al cliente ·
Honorarios y pagos · Fondos y terceros · Contabilidad de la firma (solo administrador) ·
Reportes · Licencia y suscripción (cobro con tarjeta).

Estado, prioridad y reglas de cada módulo: ver `docs/especificacion/` (una ficha por módulo).

## Reglas jurídicas

- La calculadora de plazos y términos **no se programa sin las reglas validadas por el abogado**
  (tipo de proceso, días hábiles, feriados, suspensiones).
- Debe tener pruebas automáticas con casos reales y mostrar un aviso de que el cálculo es una ayuda.
- Quién valida las reglas: el abogado (confirmar).

## Integraciones (todas POR DEFINIR hasta verificar)

- **Pagos de la suscripción:** Payphone o Datafast (cobros recurrentes: verificar soporte y comisiones).
- **SRI:** facturación electrónica (verificar costos y requisitos con la documentación oficial).
- **Firma electrónica:** el usuario usa su propio certificado; decidir dónde se firma (en su equipo o en el servidor) por seguridad.
- **SATJE (Función Judicial):** verificar si existe forma oficial de integrarse; no prometer visualización dentro de la app sin comprobarlo.
- **Correo:** Gmail, Outlook u otros (POR DEFINIR).
- **WhatsApp:** API oficial de empresa; tiene costo por conversación.

## Código legado (legacy-python/)

- Prototipo del abogado en Python, funcional pero desordenado. **Solo lectura: no modificar.**
- Uso: fuente de verdad de lo que el sistema hace hoy. Se audita y se **reescribe** en Java/React, no se traduce línea por línea.
- Resultado de la auditoría: `docs/auditoria/`.
- No contiene (ni debe contener) claves ni datos reales de clientes. Si aparecen, avisar a Juan y no usarlos.

## Diseño

- Prioridad: que sea claro, limpio y útil de verdad; el diseño actual del prototipo se considera deficiente.
- Adaptable a celular (los abogados lo usan en juzgados). Probar en Chrome, Edge, Firefox y Safari.
- Prototipar las pantallas principales (inicio, expediente, cliente, audiencias, honorarios) antes de programarlas.
- Sistema de diseño simple: pocos colores, una tipografía, espaciado coherente, tablas y formularios consistentes.

## Convenciones

- **Todo en español:** interfaz, documentos, commits y también nombres en el código (clases, variables, tablas, endpoints), por ejemplo `Expediente`, `Cliente`, `calcularPlazo`. En los identificadores no se usan tildes ni la letra ñ (`anio`, `descripcion`) para evitar problemas de codificación; los textos visibles al usuario sí llevan tildes.
- Backend con patrón MVC por capas: modelo (entidades), controlador (API), servicio (lógica de negocio) y repositorio (acceso a datos). La vista es el frontend en React. No poner lógica de negocio en los controladores.
- Pruebas obligatorias en: autenticación y roles, separación entre despachos, dinero y plazos.
- Sin código comentado ni funciones sin usar; sin dependencias nuevas sin avisar.

## Decisiones y pendientes

### Decididas
- [x] Aplicación web, no de escritorio
- [x] Backend en Java 21 con patrón MVC, desarrollado en Visual Studio Code
- [x] Frontend en React con TypeScript
- [x] Base de datos PostgreSQL
- [x] Docker y Docker Compose
- [x] Todo en español (interfaz, documentos, commits y nombres en el código)
- [x] Sin rol de cliente: solo abogados y su personal
- [x] Verificación en dos pasos incluida
- [x] Hosting inicial: servidor de un conocido de Juan
- [x] El código en Python del abogado se audita y se reescribe, no se traduce
- [x] Forma de trabajo: Juan como ingeniero revisor y Claude Code como desarrollador

### Pendientes de decidir (técnicas, decide Juan)
- [ ] Migraciones con Flyway (propuesto)
- [ ] Archivos en MinIO con Docker, o en una carpeta del servidor al inicio (propuesto)
- [ ] CI con GitHub Actions: compilar y correr pruebas en cada subida (propuesto)
- [ ] Cifrado de archivos guardados, con la clave fuera del servidor del conocido (propuesto)
- [ ] 2FA obligatoria para el administrador y opcional para los demás (propuesto)
- [ ] Versión de PostgreSQL y framework del backend (Spring Boot propuesto)
- [ ] Hosting: características del servidor, quién tiene acceso, quién hace los respaldos y cómo se protegen los datos de los clientes
- [ ] Librería de componentes del frontend

### Pendientes con el abogado
- [ ] Precio de la suscripción, si es por usuario o por despacho, y prueba gratuita
- [ ] Alcance de la primera versión vendible
- [ ] Reglas de plazos y términos por tipo de proceso, y quién las valida
- [ ] Quién da de alta a los usuarios del despacho y qué ve cada rol (en especial el asistente)
- [ ] Textos legales: términos, privacidad, responsabilidad por la calculadora
- [ ] Acuerdo entre Juan y el abogado (propiedad del código, soporte, responsabilidades)
