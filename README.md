# rpcenum

Herramienta en Bash para enumerar un dominio Active Directory a través de RPC (`rpcclient`), con sesión nula o con credenciales.

Basada en [rpcenum](https://github.com/s4vitar/rpcenum) de Marcelo Vázquez (S4vitar).

## Requisitos

- `rpcclient` (paquete `smbclient` / `samba-common-bin`)
- `awk`, `sed`, `timeout`, `mktemp` (incluidos en cualquier distribución Linux)

No necesita root.

## Instalación

```bash
git clone https://github.com/creep33/rpcenum
cd rpcenum
chmod +x rpcenum
```

## Uso

```bash
./rpcenum -e <modo> -i <host> [-u <usuario> [-p <contraseña> | -H <hash_nt>]] [-d <dominio>] [-o <fichero>]
```

| Opción | Descripción |
|--------|-------------|
| `-e` | Modo de enumeración (obligatorio) |
| `-i` | IP o hostname del controlador de dominio (obligatorio) |
| `-u` | Usuario. Si se omite, se usa sesión nula |
| `-p` | Contraseña (requiere `-u`) |
| `-H` | Hash NT para Pass-the-Hash (requiere `-u`, acepta formato `LM:NT`) |
| `-d` | Dominio |
| `-o` | Guarda la salida en un fichero, sin colores |
| `-h` | Muestra la ayuda |

### Modos

| Modo | Qué obtiene |
|------|-------------|
| `DUsers` | Usuarios del dominio y su RID |
| `DUsersInfo` | Usuarios con descripción (a menudo contienen contraseñas) |
| `DAUsers` | Miembros de Domain Admins |
| `DGroups` | Grupos del dominio con descripción |
| `PassPol` | Política de contraseñas y bloqueo de cuentas |
| `DomInfo` | Información del dominio y su SID |
| `RIDCycling` | Usuarios y grupos por fuerza bruta de RID (500-2000), útil cuando `enumdomusers` está denegado |
| `All` | Todos los modos excepto `RIDCycling` |

## Ejemplos

Sesión nula:

```bash
./rpcenum -e All -i 10.10.10.10
```

Con credenciales:

```bash
./rpcenum -e DUsersInfo -i 10.10.10.10 -u 'usuario' -p 'contraseña' -d CORP
```

Pass-the-Hash:

```bash
./rpcenum -e DAUsers -i 10.10.10.10 -u 'administrator' -H '31d6cfe0d16ae931b73c59d7e0c089c0'
```

Guardar la salida:

```bash
./rpcenum -e All -i 10.10.10.10 -o resultados.txt
```

## Notas

- Las credenciales se pasan a `rpcclient` mediante un fichero temporal con permisos `600`, así no aparecen en la lista de procesos (`ps`).
- Úsala solo contra sistemas para los que tengas autorización.

## Licencia

GPL-3.0, igual que el proyecto original.
