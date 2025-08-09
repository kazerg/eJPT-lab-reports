# Informe de Pentesting - Darkhole 2

**Máquina:** Darkhole\_2
**Autor:** Klauss Reyes Granados
**Certificación:** Preparación eJPT

---

## 🎯 Objetivo

Evaluar la seguridad de la máquina "Darkhole\_2" enfocándose en servicios web, posibles fugas de credenciales y prácticas inseguras en desarrollo.

---

## 🛰️ Recolección de Información

* IP atacante: 192.1.1.4
* Descubrimiento de red:

```bash
arp-scan -I eth0 192.1.1.0/24
```

* Escaneo Nmap:

```bash
nmap -p- -sV -sS -Pn -n 192.1.1.5
```

---

## 🔎 Enumeración

### Puerto 80 - HTTP

* Página por defecto Apache Debian.
* Fuzzing con dirb:

```bash
dirb http://192.1.1.5
```

* Se encuentran credenciales visibles en el código fuente HTML:

  * [lush@admin.com](mailto:lush@admin.com) : `321`
  * Jehad\:fool
  * Losy\:gang

---

## 💥 Explotación

* No se documenta explotación técnica detallada, pero se infiere acceso mediante credenciales encontradas en HTML.

---

## ⬆️ Escalado de Privilegios

No documentado explícitamente en el informe. Se recomienda revisar sudo, SUID y capabilities.

---

## 🚨 Vulnerabilidades Detectadas

| ID | Vulnerabilidad                      | Nivel   | Recomendación                                     |
| -- | ----------------------------------- | ------- | ------------------------------------------------- |
| 1  | Clave privada SSH accesible por web | Crítico | Eliminar archivos de claves del entorno público   |
| 2  | Contraseña débil para acceso SSH    | Alto    | Aplicar políticas de contraseñas fuertes          |
| 3  | Permisos 777 en librerías Python    | Alto    | Reducir privilegios a mínimo necesario            |
| 4  | sudo pip sin restricción            | Crítico | Limitar acceso a herramientas peligrosas con sudo |

---

## ✅ Conclusiones

* La máquina presenta múltiples debilidades comunes: credenciales expuestas, uso de contraseñas débiles y privilegios elevados sin justificación.
* Refuerza la importancia de evitar malas prácticas en el desarrollo web y revisar los permisos del sistema.

> Este informe se basa en un entorno controlado utilizado para fortalecer conocimientos previos al eJPT.
