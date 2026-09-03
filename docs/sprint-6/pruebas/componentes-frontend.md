# Pruebas de Componentes Reutilizables — Frontend

Responsable: **Emily**

## Framework

- **Jest** con preset `jest-expo` + **@testing-library/react-native** — ambos ya presentes en `package.json`; solo se agregó `test-renderer` (peer dependency obligatoria de RNTL v14 que faltaba, sin la cual RNTL no puede renderizar).
- Helper compartido `test-utils/renderWithTheme.tsx`: envuelve el componente bajo prueba en el `ThemeProvider` real de la app (requerido por `useTheme`).
- **Sin snapshots**: todas las aserciones son de comportamiento e interacción (`fireEvent.press`, `fireEvent.changeText`, estado de accesibilidad).

## Suites y casos (todos reales y en verde)

### `components/ui/__tests__/button.test.tsx`
- Normal: renderiza el label y dispara `onPress`.
- Deshabilitado: no dispara `onPress`.
- Carga: oculta el label, muestra spinner, reporta `accessibilityState.disabled` y bloquea la interacción (base de la prevención de doble envío).

### `components/ui/__tests__/formField.test.tsx`
- Renderiza etiqueta y propaga cambios de texto (callback con el valor tecleado).
- Error de validación: se muestra cuando existe y desaparece al re-renderizar sin error (datos incompletos → mensaje).
- Contador de caracteres con `maxLength` (`4/100`).

### `components/ui/__tests__/confirmationPopup.test.tsx`
- Renderiza título, descripción y ambos botones.
- Cada botón ejecuta su callback correspondiente (y solo el suyo).
- Mientras el botón primario está en carga, el secundario queda deshabilitado (no dispara su callback).
- `visible={false}` no renderiza nada.

### `components/invites/__tests__/InviteCard.test.tsx`
- Invitación recibida pendiente: muestra Aceptar/Rechazar y entrega la acción + invitación al callback.
- Invitación enviada: Cancelar abre **primero** el popup de confirmación (sin disparar la acción) y solo confirma dentro del popup dispara `cancel`.
- `busy` (otra invitación procesándose): los botones no disparan acciones.
- Estados no pendientes: no se muestran botones de acción.

### `store/__tests__/errorNotificationStore.test.ts`
- `show`: visible con duración por defecto 5 s y sin variante.
- `showSuccess`: fuerza variante `success` con duración 3 s.
- `hide`: limpia la visibilidad.

### `services/invites/__tests__/invite.service.test.ts`
- Payload correcto de creación (mensaje recortado; omitido si vacío).
- Endpoints correctos para received/sent/pending y tolerancia a payloads no-array.
- accept/decline/cancel golpean la ruta de acción correcta.
- Los errores de API se propagan al llamador (los maneja la pantalla).

## Resultado real (2026-07-18)

```
Test Suites: 7 passed, 7 total   (6 nuevas + paridad i18n preexistente)
Tests:       28 passed, 28 total
```

Cobertura (extracto real de `--coverage`): `services/invites/invite.service.ts` **100 %** statements/functions/lines. La cobertura global de `services/` es baja porque la configuración mide todos los servicios y solo los módulos nuevos tienen suite; ampliar cobertura de servicios preexistentes queda como acción del siguiente sprint.
