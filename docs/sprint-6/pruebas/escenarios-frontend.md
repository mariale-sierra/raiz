# Escenarios Funcionales de Prueba — Frontend

Responsable: **Camila**

Escenarios funcionales principales de la app, con su estado real verificado en este sprint. La verificación combina: pruebas automatizadas (Jest + React Native Testing Library, ver `componentes-frontend.md`), typecheck estricto (`tsc --noEmit`, en verde) y revisión del flujo implementado. La verificación manual sobre dispositivo contra el backend desplegado queda como paso de aceptación del equipo (la BD de Azure no era accesible desde el entorno de esta ejecución).

## 1. Inicio de sesión
- Login con credenciales válidas → token en SecureStore, redirección a tabs. **Implementado (sprints previos); cubierto por interceptor + authContext.**
- Credenciales inválidas → mensaje de error sin crash. **Implementado.**
- Restauración de sesión al reabrir: token rechazado (401) limpia sesión; error de red la preserva. **Implementado (Sprint 5).**

## 2. Challenges
- Visualización de activos/explorar, join con confirmación, leave. **Implementado (sprints previos).**
- Tras aceptar una invitación, el challenge aparece en "Mis challenges" sin reiniciar la app (invalidación de caché de progreso). **Nuevo este sprint — verificado por diseño del hook `useInvites` + `invalidateChallengeProgressCache`.**

## 3. Flujo de workout
- Registro de progreso diario, un log por día, evidencia con cámara. **Implementado (sprints previos).**

## 4. Perfil
- Carga del perfil real (`GET /users/me/profile`) con foto, nombre y bio. **Nuevo — implementado.**
- Recarga al volver a la pantalla (useFocusEffect): los cambios persisten al reabrir. **Nuevo — implementado.**

## 5. Edición del perfil
- Carga de datos actuales en el formulario. **Implementado.**
- Validación: nombre vacío bloquea guardado con mensaje; contadores de caracteres (150 nombre / 1000 bio). **Implementado; el contador se prueba automatizadamente en FormField.**
- Guardado parcial: solo se envían los campos modificados (PATCH). **Implementado.**
- Cambio de foto: selector de imagen → subida por URL firmada R2 → persistencia; estados de carga y error. **Implementado reutilizando `uploadImageAsync`.**
- Cambio de privacidad con toast de confirmación. **Implementado.**
- Error del servidor al guardar → toast de error, formulario conserva datos. **Implementado.**

## 6. Invitaciones — recibidas
- Lista con challenge, remitente, mensaje y estado. **Implementado.**
- Aceptar → toast de éxito, listas refrescadas, challenge agregado. **Implementado; transición de estado probada automatizadamente en InviteCard.**
- Rechazar → toast, estado "Rechazada". **Implementado.**
- Estado vacío ("Aún no has recibido invitaciones"), carga (spinner) y error con reintento. **Implementado.**
- Pull-to-refresh (patrón ya usado en la app). **Implementado.**

## 7. Invitaciones — enviadas
- Lista con destinatario y estado; cancelar solo pendientes, con popup de confirmación. **Implementado; el popup previo a cancelar está probado automatizadamente.**

## 8. Envío de invitaciones
- Desde el detalle del challenge (ícono visible solo para miembros/creador) → búsqueda por username con debounce → confirmación → envío. **Implementado.**
- Doble envío bloqueado (botón "Enviada" deshabilitado tras éxito; `sendingId` bloquea envíos concurrentes). **Implementado; patrón probado en tests de Button.**
- Duplicado (409 del backend) → mensaje específico, no error técnico crudo. **Implementado.**

## 9. Estados transversales
- Carga: spinners en pantallas y botones (`loading` de Button probado automatizadamente).
- Vacío: mensajes dedicados en invitaciones y búsqueda.
- Error de servidor: interceptor global + toasts contextuales; nunca se muestra el error crudo del backend.
- Éxito: toasts verdes nuevos (variante `success` del sistema de notificaciones, probada automatizadamente).

## Resultados de la ejecución automatizada (real, 2026-07-18)

```
Test Suites: 7 passed, 7 total
Tests:       28 passed, 28 total
```
