# Ansible lab for classrooms: configure a machine with a playbook

[English](README.md) | [Français](README.fr.md) | [Español](README.es.md) | [日本語](README.ja.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-ansible-basics/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-ansible-basics/actions/workflows/check.yml)

A ready-made **Ansible lab** for teaching infrastructure as code, from the [MitsuruTech Learning Platform](https://mitsurutech.co.uk/learning), where schools and colleges give every student their own machines and terminal in the browser. Each student gets an Ubuntu VM with **Ansible** installed and a project with an unfinished **playbook**. They finish it to **install a package**, **make a file from a Jinja2 template with a variable** and **write a systemd unit file**, then run it a second time and see `changed=0`: that is **idempotence**, the idea that makes Ansible worth learning. Nothing is installed on the student's computer.

## What the student does

The lab leaves a project in `~/ansible-lab` (an `inventory.ini` for this one machine over a local connection, an `ansible.cfg`, a template and a half-written `site.yml` with three TODOs) and a `README.md` in the home folder that says what to do and gives none of the answers:

1. Install a package with a package module.
2. Make `/etc/ansible-lab/welcome.txt` from the template, owned by root, mode 0644.
3. Write a systemd unit file whose description carries the site name variable.
4. Run the playbook again: a second run must report `changed=0`.

## How it is marked

The lab has a **check**, a script that marks a handed-in VM without the teacher reading it. It runs on a copy of the saved machine after a fresh start. It reads the YAML and refuses a playbook of `shell`, `command`, `raw` or `script` tasks, includes and roles. It reads the saved package and files, then **runs the student's playbook twice with a site name nobody has seen**: what the playbook makes must carry it, which a file written by hand does not, and the second run must report `changed=0`, which a task that changes something every time does not. The first line of what it prints says what is not right.

What it does not do: it reads the unit file and never starts the service, and a student with root on their own VM could still tamper with Ansible itself. It is a check for a classroom, not a proof.

## What is in the file

`ansible-basics.lab.yaml` is a whole **VM lab definition**, exactly what the platform runs:

| Key | What it is |
| --- | --- |
| `cpus`, `memory`, `disk` | the size of each student's VM: 1 CPU, 1024 MB and 10 GB |
| `open` | the ports that open in the browser: none, the lab is the terminal |
| `setup` | a script run once, as root, the first time each student's VM starts: it installs `ansible-core` from Ubuntu's archive (about ten seconds) and writes the project and the README |
| `check` | the marking script, run as root on a copy of the saved disk after a fresh boot; its exit status is the result and `0` passes |

## Using it on the platform

**To set it as work for a class:**

1. In your module, add a task and choose the **Ubuntu VM lab** card.
2. Press the Ansible sample, or paste the contents of `ansible-basics.lab.yaml`. Using a sample replaces the text, and Undo brings yours back.
3. Publish and try your own copy first. Every student gets a VM of their own, kept between sessions.
4. Assign it like any task. When it is handed in, run the check on a student's row in the class list: it starts a copy of their saved machine that is not saved back, and shows whether it passed and what it printed.

The help manual's [page for this lab](https://mitsurutech.co.uk/help/lab-ansible-basics), its [guide to Ubuntu VM labs](https://mitsurutech.co.uk/help/labs) and its [sample labs page](https://mitsurutech.co.uk/help/sample-labs) cover the rest.

No college account yet? [Your first year is free](https://mitsurutech.co.uk/pricing) for up to 50 students, and [signing up](https://mitsurutech.co.uk/signup) takes an email address.

## Checking the file

On every push, `.github/workflows/check.yml` checks that the file is valid YAML with the five keys above and that `setup` and `check` are each inside the platform's 16 KB limit. It does not boot a VM: that is done on the platform's own machines.

## What does not work in a lab

The VM reaches Ubuntu's package archive, PyPI, npm and GitHub, and nothing else. That is why this lab is **Ansible only**: Terraform downloads its providers from a registry the VM cannot reach, so it is not offered. The target is the VM itself, over a local connection, since it is one machine.

## About

Made by [Mitsuru Technologies](https://mitsurutech.co.uk) for the [MitsuruTech Learning Platform](https://mitsurutech.co.uk/learning): Python, Java, Go, Node, SQL and Linux in the browser for schools and colleges, with container labs, VM labs, web apps deployed from GitHub, and quizzes that mark themselves. Try code with no account in the [playground](https://mitsurutech.co.uk/playground).

More labs from the same place: [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea) and [lab-sample](https://github.com/mitsurutech/lab-sample).

## Licence

This work is licensed under [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). You may share it and adapt it for learning and teaching, if you credit Mitsuru Technologies Ltd, do not use it commercially, and share anything you make from it under the same licence. The full text is in the `LICENSE` file.

[Ansible](https://www.ansible.com) (`ansible-core`, GPL-3.0) is installed from Ubuntu's archive when a VM starts; nothing of it is distributed here.
