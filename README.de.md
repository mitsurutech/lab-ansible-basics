# Ansible-Lab für den Unterricht: eine Maschine mit einem Playbook konfigurieren

[English](README.md) | [Français](README.fr.md) | [Español](README.es.md) | [日本語](README.ja.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-ansible-basics/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-ansible-basics/actions/workflows/check.yml)

Ein fertiges **Ansible-Lab** für den Unterricht zu Infrastructure as Code, von der [MitsuruTech Learning Platform](https://mitsurutech.co.uk/de/learning), auf der Schulen und Colleges allen Lernenden eigene Maschinen und ein Terminal im Browser geben. Alle Lernenden erhalten eine Ubuntu-VM mit installiertem **Ansible** und ein Projekt mit einem unfertigen **Playbook**. Sie vervollständigen es, um **ein Paket zu installieren**, **eine Datei aus einer Jinja2-Vorlage mit einer Variablen zu erzeugen** und **eine systemd-Unit-Datei zu schreiben**, führen es dann ein zweites Mal aus und sehen `changed=0`: Das ist **Idempotenz**, die Idee, wegen der sich Ansible zu lernen lohnt. Auf dem Rechner der Lernenden wird nichts installiert.

## Was die Lernenden tun

Das Lab hinterlässt ein Projekt in `~/ansible-lab` (eine `inventory.ini` für genau diese Maschine über eine lokale Verbindung, eine `ansible.cfg`, eine Vorlage und eine halb geschriebene `site.yml` mit drei TODOs) und im Home-Verzeichnis eine `README.md`, die sagt, was zu tun ist, und keine der Lösungen verrät:

1. Ein Paket mit einem Paketmodul installieren.
2. `/etc/ansible-lab/welcome.txt` aus der Vorlage erzeugen, Eigentümer root, Modus 0644.
3. Eine systemd-Unit-Datei schreiben, deren Beschreibung die Variable mit dem Namen der Website enthält.
4. Das Playbook erneut ausführen: Ein zweiter Lauf muss `changed=0` melden.

## Wie bewertet wird

Das Lab hat einen **Check**, ein Skript, das eine abgegebene VM bewertet, ohne dass die Lehrkraft sie lesen muss. Er läuft nach einem frischen Start auf einer Kopie der gespeicherten Maschine. Er liest das YAML und lehnt ein Playbook aus `shell`-, `command`-, `raw`- oder `script`-Tasks, Includes und Rollen ab. Er liest das gespeicherte Paket und die Dateien und **führt dann das Playbook der Lernenden zweimal mit einem Website-Namen aus, den noch niemand gesehen hat**: Was das Playbook erzeugt, muss diesen Namen enthalten, was eine von Hand geschriebene Datei nicht tut, und der zweite Lauf muss `changed=0` melden, was ein Task, der jedes Mal etwas ändert, nicht tut. Die erste Zeile seiner Ausgabe sagt, was nicht stimmt.

Was er nicht tut: Er liest die Unit-Datei und startet den Dienst nie, und Lernende mit root auf ihrer eigenen VM könnten Ansible selbst manipulieren. Es ist ein Check für den Unterricht, kein Beweis.

## Was in der Datei steht

`ansible-basics.lab.yaml` ist eine vollständige **VM-Lab-Definition**, genau das, was die Plattform ausführt:

| Schlüssel | Was er ist |
| --- | --- |
| `cpus`, `memory`, `disk` | die Größe der VM aller Lernenden: 1 CPU, 1024 MB und 10 GB |
| `open` | die Ports, die sich im Browser öffnen: keine, das Lab ist das Terminal |
| `setup` | ein Skript, das einmal als root läuft, wenn die VM der Lernenden zum ersten Mal startet: Es installiert `ansible-core` aus dem Ubuntu-Archiv (etwa zehn Sekunden) und schreibt das Projekt und die README |
| `check` | das Bewertungsskript, das nach einem frischen Start als root auf einer Kopie der gespeicherten Festplatte läuft; sein Exit-Status ist das Ergebnis, und `0` besteht |

## Auf der Plattform verwenden

**So stellen Sie es einer Klasse als Aufgabe:**

1. Fügen Sie in Ihrem Modul eine Aufgabe hinzu und wählen Sie die Karte **Ubuntu-VM-Lab**.
2. Drücken Sie auf das Ansible-Beispiel oder fügen Sie den Inhalt von `ansible-basics.lab.yaml` ein. Ein Beispiel ersetzt den Text, und Rückgängig holt Ihren zurück.
3. Veröffentlichen Sie es und probieren Sie zuerst Ihre eigene Kopie aus. Alle Lernenden erhalten eine eigene VM, die zwischen den Sitzungen erhalten bleibt.
4. Weisen Sie es wie jede Aufgabe zu. Nach der Abgabe führen Sie den Check in der Zeile einer lernenden Person in der Klassenliste aus: Er startet eine Kopie ihrer gespeicherten Maschine, die nicht zurückgespeichert wird, und zeigt, ob sie bestanden hat und was ausgegeben wurde.

Die [Seite des Handbuchs zu diesem Lab](https://mitsurutech.co.uk/de/help/lab-ansible-basics), seine [Anleitung zu Ubuntu-VM-Labs](https://mitsurutech.co.uk/de/help/labs) und seine [Seite mit Beispiel-Labs](https://mitsurutech.co.uk/de/help/sample-labs) behandeln den Rest.

Noch kein Schulkonto? [Ihr erstes Jahr ist kostenlos](https://mitsurutech.co.uk/de/pricing) für bis zu 50 Schülerinnen und Schüler, und für die [Registrierung](https://mitsurutech.co.uk/de/signup) genügt eine E-Mail-Adresse.

## Die Datei prüfen

Bei jedem Push prüft `.github/workflows/check.yml`, dass die Datei gültiges YAML mit den fünf oben genannten Schlüsseln ist und dass `setup` und `check` jeweils innerhalb der 16-KB-Grenze der Plattform liegen. Eine VM wird dabei nicht gestartet: Das geschieht auf den eigenen Maschinen der Plattform.

## Was in einem Lab nicht funktioniert

Die VM erreicht das Paketarchiv von Ubuntu, PyPI, npm und GitHub und sonst nichts. Deshalb ist dieses Lab **nur Ansible**: Terraform lädt seine Provider aus einer Registry, die die VM nicht erreichen kann, und wird deshalb nicht angeboten. Das Ziel ist die VM selbst, über eine lokale Verbindung, da es nur eine Maschine ist.

## Über dieses Projekt

Erstellt von [Mitsuru Technologies](https://mitsurutech.co.uk/de) für die [MitsuruTech Learning Platform](https://mitsurutech.co.uk/de/learning): Python, Java, Go, Node, SQL und Linux im Browser für Schulen und Colleges, mit Container-Labs, VM-Labs, aus GitHub bereitgestellten Web-Apps und Quizzen, die sich selbst bewerten. Probieren Sie Code ohne Konto im [Playground](https://mitsurutech.co.uk/de/playground) aus.

Weitere Labs aus demselben Haus: [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea) und [lab-sample](https://github.com/mitsurutech/lab-sample).

## Lizenz

Dieses Werk steht unter der Lizenz [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Sie dürfen es zum Lernen und Unterrichten teilen und bearbeiten, sofern Sie Mitsuru Technologies Ltd nennen, es nicht kommerziell nutzen und alles, was Sie daraus machen, unter derselben Lizenz teilen. Der vollständige Text steht in der Datei `LICENSE`.

[Ansible](https://www.ansible.com) (`ansible-core`, GPL-3.0) wird beim Start einer VM aus dem Ubuntu-Archiv installiert; nichts davon wird hier verbreitet.
