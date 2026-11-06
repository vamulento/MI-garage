# Mi Garage

App web instalable (PWA) para llevar tus vehículos: ITV, seguro, taller y combustible.
Es un sitio estático (`index.html`, `sw.js`, `manifest.webmanifest` e iconos) que se publica en Vercel.

## Cuentas y datos

- Entras con **correo y contraseña** (Supabase Auth). Tus datos se guardan en tu cuenta y se sincronizan entre dispositivos. Cada dispositivo guarda además una copia para usarla sin conexión; los cambios se suben al volver.
- **Solo pueden usar la app los correos autorizados.** La lista está en la tabla `garage_allowed_emails` del proyecto de Supabase. Aunque alguien consiga crear una cuenta, las reglas de la base de datos (RLS) no le dejan leer ni escribir nada de Mi Garage.
- Cada usuario ve solo su fila de la tabla `garage_data`.
- Al pulsar **Salir** se borran los datos de ese dispositivo (siguen en tu cuenta).
- Si cambian los datos en otro dispositivo mientras trabajas sin conexión, gana lo de la cuenta y se descarga una copia de tus cambios locales.
- La clave de consulta por matrícula (apivehiculo.com) se guarda solo en cada dispositivo, nunca en la nube.

## Dar acceso a otra persona (solo por invitación)

El registro abierto de Supabase está **desactivado** (Authentication → Sign In / Providers → "Allow new users to sign up"), así que nadie puede crear cuenta por su cuenta. El alta funciona así:

1. La persona pulsa **Solicitar acceso** en la pantalla de entrada y escribe su correo.
2. La función `garage-access` (Edge Function de Supabase) guarda la solicitud en `garage_requests` y te avisa por **Telegram** con un enlace firmado.
3. Abres el enlace (`/#/aprobar?...`) y pulsas **Aprobar y enviar invitación**. Eso añade el correo a `garage_allowed_emails` y Supabase le envía un correo de invitación.
4. La persona abre el correo, elige su contraseña y ya puede entrar.

**Ajustes necesarios en Supabase** (solo se hacen desde el panel):
- Edge Functions → Secrets: `TELEGRAM_BOT_TOKEN` y `TELEGRAM_CHAT_ID` (el bot y el chat donde quieres los avisos).
- Authentication → URL Configuration → Redirect URLs: añadir `https://garage.llaga.es/**` (y `https://mi-garage-one.vercel.app/**` si la usas), o la invitación abrirá otra dirección.

Alternativa manual, sin Telegram: en Authentication → Users → "Invite user" y después, en el editor SQL:

```sql
insert into public.garage_allowed_emails (email) values ('correo@ejemplo.com');
```

Para quitar el acceso a alguien: borra su correo de `garage_allowed_emails` (conserva su cuenta pero no puede leer ni escribir datos).

## Copias de seguridad

Ajustes > Copia completa (JSON) descarga todo. "Importar copia" sustituye los datos actuales por los de un archivo. Para traer los datos de la versión anterior (que guardaba todo solo en el navegador), descarga la copia allí e impórtala aquí tras entrar.

## Publicación

Cada cambio subido a la rama `main` de GitHub se despliega solo en Vercel.
