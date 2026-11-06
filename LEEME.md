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

## Autorizar a otra persona

En Supabase, abre el editor SQL del proyecto y ejecuta (con el correo en minúsculas):

```sql
insert into public.garage_allowed_emails (email) values ('correo@ejemplo.com');
```

Después esa persona entra en la app, pulsa **Crear cuenta** y elige su contraseña.

## Copias de seguridad

Ajustes > Copia completa (JSON) descarga todo. "Importar copia" sustituye los datos actuales por los de un archivo. Para traer los datos de la versión anterior (que guardaba todo solo en el navegador), descarga la copia allí e impórtala aquí tras entrar.

## Publicación

Cada cambio subido a la rama `main` de GitHub se despliega solo en Vercel.
