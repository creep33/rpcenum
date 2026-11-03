# rpcenum

Bash tool to enumerate an Active Directory domain over RPC (`rpcclient`), using a null session or credentials.

Based on [rpcenum](https://github.com/s4vitar/rpcenum) by Marcelo Vázquez (S4vitar).

## Requirements

- `rpcclient` (package `smbclient` / `samba-common-bin`)
- `awk`, `sed`, `timeout`, `mktemp` (included in any Linux distribution)

Root is not required.

## Installation

```bash
git clone https://github.com/creep33/rpcenum
cd rpcenum
chmod +x rpcenum
```

## Usage

```bash
./rpcenum -e <mode> -i <host> [-u <username> [-p <password> | -H <nt_hash>]] [-d <domain>] [-o <file>]
```

| Option | Description |
|--------|-------------|
| `-e` | Enumeration mode (required) |
| `-i` | Domain controller IP or hostname (required) |
| `-u` | Username. A null session is used if omitted |
| `-p` | Password (requires `-u`) |
| `-H` | NT hash for Pass-the-Hash (requires `-u`, `LM:NT` format accepted) |
| `-d` | Domain |
| `-o` | Save the output to a file, without colours |
| `-h` | Show the help panel |

### Modes

| Mode | What it gets |
|------|--------------|
| `DUsers` | Domain users and their RID |
| `DUsersInfo` | Users with a description (descriptions often contain passwords) |
| `DAUsers` | Members of Domain Admins |
| `DGroups` | Domain groups with their description |
| `PassPol` | Password and account lockout policy |
| `DomInfo` | Domain information and SID |
| `RIDCycling` | Users and groups found by RID brute force (500-2000), useful when `enumdomusers` is denied |
| `All` | All modes except `RIDCycling` |

## Examples

Null session:

```bash
./rpcenum -e All -i 10.10.10.10
```

With credentials:

```bash
./rpcenum -e DUsersInfo -i 10.10.10.10 -u 'user' -p 'password' -d CORP
```

Pass-the-Hash:

```bash
./rpcenum -e DAUsers -i 10.10.10.10 -u 'administrator' -H '31d6cfe0d16ae931b73c59d7e0c089c0'
```

Save the output:

```bash
./rpcenum -e All -i 10.10.10.10 -o results.txt
```

## Notes

- Credentials are passed to `rpcclient` through a temporary file with `600` permissions, so they don't show up in the process list (`ps`).
- Only use it against systems you are authorised to test.

## License

GPL-3.0, same as the original project.
