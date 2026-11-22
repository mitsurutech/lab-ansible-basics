# Laboratorio de Ansible para el aula: configurar una máquina con un playbook

[English](README.md) | [Français](README.fr.md) | [Español](README.es.md) | [日本語](README.ja.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-ansible-basics/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-ansible-basics/actions/workflows/check.yml)

Un **laboratorio de Ansible** listo para usar para enseñar infraestructura como código, de la [plataforma de aprendizaje MitsuruTech](https://mitsurutech.co.uk/es/learning), donde los centros educativos dan a cada alumno sus propias máquinas y su terminal en el navegador. Cada alumno recibe una VM de Ubuntu con **Ansible** instalado y un proyecto con un **playbook** sin terminar. Lo completa para **instalar un paquete**, **crear un archivo a partir de una plantilla Jinja2 con una variable** y **escribir un archivo de unidad de systemd**, y después lo ejecuta una segunda vez y ve `changed=0`: eso es la **idempotencia**, la idea que hace que merezca la pena aprender Ansible. No se instala nada en el ordenador del alumno.

## Qué hace el alumno

El laboratorio deja un proyecto en `~/ansible-lab` (un `inventory.ini` para esta única máquina con conexión local, un `ansible.cfg`, una plantilla y un `site.yml` a medio escribir con tres TODO) y un `README.md` en la carpeta personal que explica qué hacer y no da ninguna respuesta:

1. Instalar un paquete con un módulo de paquetes.
2. Crear `/etc/ansible-lab/welcome.txt` a partir de la plantilla, con root como propietario y modo 0644.
3. Escribir un archivo de unidad de systemd cuya descripción lleve la variable del nombre del sitio.
4. Volver a ejecutar el playbook: una segunda ejecución debe mostrar `changed=0`.

## Cómo se califica

El laboratorio tiene una **comprobación**, un script que califica una VM entregada sin que el profesor tenga que revisarla. Se ejecuta sobre una copia de la máquina guardada tras un arranque limpio. Lee el YAML y rechaza un playbook hecho de tareas `shell`, `command`, `raw` o `script`, inclusiones y roles. Lee el paquete y los archivos guardados, y después **ejecuta dos veces el playbook del alumno con un nombre de sitio que nadie ha visto**: lo que el playbook crea debe contenerlo, algo que un archivo escrito a mano no cumple, y la segunda ejecución debe mostrar `changed=0`, algo que una tarea que cambia algo cada vez no cumple. La primera línea de lo que imprime dice qué no está bien.

Lo que no hace: lee el archivo de unidad pero nunca inicia el servicio, y un alumno con root en su propia VM aún podría manipular el propio Ansible. Es una comprobación para el aula, no una prueba.

## Qué hay en el archivo

`ansible-basics.lab.yaml` es una **definición de laboratorio de VM** completa, exactamente lo que ejecuta la plataforma:

| Clave | Qué es |
| --- | --- |
| `cpus`, `memory`, `disk` | el tamaño de la VM de cada alumno: 1 CPU, 1024 MB y 10 GB |
| `open` | los puertos que se abren en el navegador: ninguno, el laboratorio es el terminal |
| `setup` | un script que se ejecuta una vez, como root, la primera vez que arranca la VM de cada alumno: instala `ansible-core` desde el archivo de Ubuntu (unos diez segundos) y escribe el proyecto y el README |
| `check` | el script de calificación, que se ejecuta como root sobre una copia del disco guardado tras un arranque limpio; su estado de salida es el resultado y `0` significa aprobado |

## Usarlo en la plataforma

**Para asignarlo como trabajo a una clase:**

1. En tu módulo, añade una tarea y elige la tarjeta **Laboratorio de VM Ubuntu**.
2. Pulsa el ejemplo de Ansible o pega el contenido de `ansible-basics.lab.yaml`. Usar un ejemplo reemplaza el texto, y Deshacer recupera el tuyo.
3. Publica y prueba primero tu propia copia. Cada alumno tiene una VM propia, que se conserva entre sesiones.
4. Asígnalo como cualquier tarea. Cuando se entregue, ejecuta la comprobación en la fila de un alumno en la lista de la clase: arranca una copia de su máquina guardada que no se guarda de vuelta, y muestra si aprobó y qué imprimió.

La [página de este laboratorio](https://mitsurutech.co.uk/es/help/lab-ansible-basics) del manual, su [guía de los laboratorios de VM Ubuntu](https://mitsurutech.co.uk/es/help/labs) y su [página de laboratorios de ejemplo](https://mitsurutech.co.uk/es/help/sample-labs) cubren el resto.

¿Todavía no tienes una cuenta para tu centro? [El primer año es gratis](https://mitsurutech.co.uk/es/pricing) hasta 50 alumnos, y [crear una cuenta](https://mitsurutech.co.uk/es/signup) solo requiere una dirección de correo electrónico.

## Comprobar el archivo

En cada push, `.github/workflows/check.yml` comprueba que el archivo es un YAML válido con las cinco claves anteriores y que `setup` y `check` caben cada uno en el límite de 16 KB de la plataforma. No arranca ninguna VM: eso se hace en las máquinas de la plataforma.

## Qué no funciona en un laboratorio

La VM llega al archivo de paquetes de Ubuntu, PyPI, npm y GitHub, y a nada más. Por eso este laboratorio es **solo de Ansible**: Terraform descarga sus proveedores de un registro al que la VM no puede llegar, así que no se ofrece. El destino es la propia VM, con conexión local, ya que es una sola máquina.

## Acerca de

Hecho por [Mitsuru Technologies](https://mitsurutech.co.uk/es) para la [plataforma de aprendizaje MitsuruTech](https://mitsurutech.co.uk/es/learning): Python, Java, Go, Node, SQL y Linux en el navegador para colegios y centros educativos, con laboratorios de contenedores, laboratorios de VM, aplicaciones web desplegadas desde GitHub y cuestionarios que se califican solos. Prueba código sin cuenta en el [entorno de pruebas](https://mitsurutech.co.uk/es/playground).

Más laboratorios del mismo sitio: [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea) y [lab-sample](https://github.com/mitsurutech/lab-sample).

## Licencia

Esta obra está bajo licencia [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Puedes compartirla y adaptarla para aprender y enseñar, si citas a Mitsuru Technologies Ltd, no la usas comercialmente y compartes lo que hagas con ella bajo la misma licencia. El texto completo está en el archivo `LICENSE`.

[Ansible](https://www.ansible.com) (`ansible-core`, GPL-3.0) se instala desde el archivo de Ubuntu cuando arranca una VM; aquí no se distribuye nada de él.
