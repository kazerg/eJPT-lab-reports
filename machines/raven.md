# Informe de Pentesting - Raven

**Máquina:** Raven
**Autor:** Klauss Reyes Granados
**Certificación:** Preparación eJPT

---

## 🎯 Objetivo

Simular un ataque externo contra la máquina Raven, centrado en WordPress, SSH y escalado básico en entornos Linux.

---

## 🛰️ Recolección de Información

* Red: 192.1.1.0/24
* IP objetivo: 192.1.1.10

### Escaneo Nmap:

```bash
nmap -p- -sV -sS -Pn -n 192.1.1.10
```

---

## 🔎 Enumeración

### Puerto 80 - HTTP

* Página por defecto Apache.
* WordPress localizado con:

```bash
dirb http://192.1.1.10/wordpress
```

* Usuarios detectados:

  * michael
  * steven

---

## 💥 Explotación

### SSH Enumeration (CVE-2018-15473)

```bash
./ssh-username-enum.py -t 10 -w wordlist_raven raven.local
```

* Acceso exitoso:

  * michael\:michael
  * root\:R\@v3nSecurity
  * steven\:pink84

---

## ⬆️ Escalado de Privilegios

* Shell interactiva:

```bash
sudo python -c 'import pty; pty.spawn("/bin/bash")'
```

---

## 🚨 Vulnerabilidades Detectadas

| ID | Vulnerabilidad                             | Nivel   | Recomendación                                          |
| -- | ------------------------------------------ | ------- | ------------------------------------------------------ |
| 1  | Enumeración de usuarios SSH                | Alto    | Parchear versión de OpenSSH                            |
| 2  | Contraseñas débiles (acceso root directo)  | Crítico | Implementar políticas de seguridad robustas            |
| 3  | Uso de Python desde sudo sin restricciones | Alto    | Limitar comandos sudo accesibles por usuarios normales |

---

## ✅ Conclusiones

* Acceso completo obtenido a través de combinación de WordPress, enumeración de usuarios SSH y contraseñas débiles.
* Escalada básica mediante shell Python.

> Raven es una máquina sencilla y efectiva para practicar enumeración combinada entre web y SSH, escalada básica y configuración mínima de servicios inseguros.
