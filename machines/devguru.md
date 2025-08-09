# Informe de Pentesting - Devguru

**Máquina:** Devguru
**Autor:** Klauss Reyes Granados
**Certificación:** Preparación eJPT

---

## 🎯 Objetivo

Analizar la máquina Devguru orientada a la exposición de repositorios Git, credenciales en texto claro y explotación de CMS.

---

## 🛰️ Recolección de Información

* IP: 192.1.1.4
* Escaneo:

```bash
nmap -p- -sV -sS -Pn -n 192.1.1.6
```

---

## 🔎 Enumeración

### Puerto 80 - HTTP

* Página por defecto de Apache.
* Directorios descubiertos:

  * `/.git/HEAD` → contiene referencia a `refs/heads/master`
  * `/backend` → CMS October

### Puerto 8585 - Git vía HTTP

* Acceso al usuario `frank`

---

## 💥 Explotación

### 1. Dumpear Git expuesto

```bash
python git_dumper.py http://192.1.1.6/.git/ devguru-repo
```

* Se obtienen archivos PHP (`adminer.php`, `config`, etc.)

### 2. Acceso a Adminer

* Configuración de base de datos encontrada
* Contraseña hasheada con bcrypt
* Se genera nueva contraseña `password1234`, se rehashea, y se actualiza en la base

### 3. CMS October → Reverse shell

* Se inyecta payload PHP ofuscado mediante JavaScript

```js
var myVar = '<?php shell code... ?>';
document.write(myVar);
```

* Se obtiene shell remota

### 4. Backups

```bash
locate backups
ls -l /var/backups
```

* Se encuentran archivos de configuración con más credenciales

### 5. Gitea

* Se accede a Gitea y se modifican Git Hooks para lanzar nueva reverse shell como usuario `frank`

---

## ⬆️ Escalado de Privilegios

### Vulnerabilidad en `sudo` 1.8.21p2

```bash
sudo -u#1 /usr/bin/sqlite3 /dev/null '.shell /bin/bash'
```

* Se obtiene shell como root

---

## 🚨 Vulnerabilidades Detectadas

| ID | Vulnerabilidad                     | Nivel   | Recomendación                                        |
| -- | ---------------------------------- | ------- | ---------------------------------------------------- |
| 1  | Contraseñas en claro en config.php | Crítico | Usar gestores de secretos, eliminar backups antiguos |
| 2  | Git expuesto vía HTTP              | Crítico | Bloquear acceso a `.git/` en producción              |
| 3  | Vulnerabilidad sudo 1.8.21p2       | Crítico | Actualizar paquetes críticos del sistema             |

---

## ✅ Conclusiones

* Esta máquina destaca por representar errores comunes de despliegue y configuración.
* Permite practicar desde Git leaking, modificaciones en base de datos y ejecución de reverse shells.
* Se logra acceso root aprovechando una versión vulnerable de `sudo`.

> Un excelente escenario para practicar técnicas modernas de pentesting ofensivo en aplicaciones web integradas a repos y entornos reales.
