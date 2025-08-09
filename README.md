# 🛡️ Informes de Pentesting - Preparación eJPT

Este repositorio contiene una colección de informes técnicos de pentesting elaborados por **Klauss Reyes Granados** como parte de su preparación para la certificación **eLearnSecurity Junior Penetration Tester (eJPT)**.

Cada informe documenta el proceso completo de intrusión sobre máquinas vulnerables en entornos controlados, siguiendo las fases clásicas de un pentest:

* 🔍 Recolección de información
* 📡 Enumeración de servicios
* 💥 Explotación
* ⬆️ Escalada de privilegios
* 🧠 Post-explotación
* ✅ Recomendaciones y conclusiones

---

## 📂 Estructura del Repositorio

```plaintext
ejpt-pentesting-reports/
├── README.md
├── .gitignore
├── machines/
│   ├── basic_pentesting.md
│   ├── brainpan.md
│   ├── breakout.md
│   ├── chronos.md
│   ├── darkhole.md
│   ├── devguru.md
│   ├── empire_lupinone.md
│   └── raven.md
└── assets/
    └── screenshots/
        ├── basic_pentesting/          # ifconfig, arp-scan, hydra, escalada pip
        ├── brainpan/                  # Immunity Debugger, badchars, shellcode
        ├── breakout/                  # código fuente en brainfuck, webmin, reverse shell
        ├── chronos/                   # enum SSH, usuarios válidos, ataque con hydra
        ├── darkhole/                  # dirb, credenciales expuestas, smbmap
        ├── devguru/                   # git-dumper, adminer, reverse shell, gitea
        ├── empire_lupinone/           # wfuzz, base58, ssh2john, linPEAS, pip exploit
        └── raven/                     # cve-2018-15473, wordpress dirb, SSH acceso root
```

---

## 🧠 Máquinas incluidas

| Máquina              | Técnicas Destacadas                                    |
| -------------------- | ------------------------------------------------------ |
| **Basic Pentesting** | Enumeración de servicios, acceso SSH, escalado por pip |
| **Brainpan**         | Explotación Buffer Overflow, creación de shellcode     |
| **Breakout**         | Análisis de servicios Webmin, reverse shell vía web    |
| **Chronos**          | Ataque a WordPress, fuerza bruta SSH                   |
| **Darkhole**         | Fuzzing web, credenciales duras en HTML                |
| **Devguru**          | Dumpeo de repos Git, acceso a CMS October, Gitea       |
| **Empire LupinOne**  | SSH por clave, abuso de librerías Python y sudo pip    |
| **Raven**            | Enumeración WordPress, explotación SSH, escalada bash  |

---

## 📸 Capturas de Pantalla

Dentro de `assets/screenshots/` encontrarás directorios específicos por máquina que contienen (o están preparados para contener) capturas relevantes:

### Ejemplos por máquina:

* `basic_pentesting/`: salida de `ifconfig`, escaneo `arp-scan`, ataque SSH con `hydra`, ejecución de `pip` con `sudo`.
* `brainpan/`: crash en Immunity Debugger, offset, badchars, shellcode generado con msfvenom.
* `breakout/`: código en brainfuck traducido, login Webmin, uso de reverse shell vía panel web.
* `chronos/`: wordlist SSH, usernames válidos detectados, conexión exitosa con `hydra`.
* `darkhole/`: enumeración con `dirb`, extracción de credenciales desde HTML.
* `devguru/`: estructura de Git dumpeado, configuración en Adminer, shell PHP inyectada en backend.
* `empire_lupinone/`: detección de clave base58, explotación SSH privada, shell con pip como root.
* `raven/`: enumeración WordPress, usuarios válidos, explotación de CVE y acceso root.

---

## 🧾 Licencia

Este contenido es de uso educativo y fue desarrollado sobre entornos intencionadamente vulnerables. **No debe ser utilizado en entornos de producción sin autorización explícita.**

---

> Preparado por **Klauss Reyes Granados** - 2024
