# Ansible Homework

## 1. Projekt leírása
Ez a projekt egy Ansible playbook segítségével automatikusan telepíti és konfigurálja az Nginx webkiszolgálót egy távoli szerveren. A Vagrant használatával egy **Ansible** és egy **Web** virtuális gép kerül beállításra.

---

## 2. Követelmények
A projekt futtatásához szükséges:
- **Vagrant** (https://www.vagrantup.com/)
- **VirtualBox** (https://www.virtualbox.org/)
- **Ansible** (automatikusan telepítésre kerül az Ansible VM-en)

---

## 3. Használat

### 3.1. Virtuális gépek létrehozása
Nyisd meg a terminált, navigálj a projekt mappájába, majd futtasd az alábbi parancsot:
```bash
vagrant up
```
Ez létrehozza és beállítja az **Ansible** és **Web** virtuális gépeket.

### 3.2. Ansible playbook futtatása
Miután a gépek elindultak, lépj be az **Ansible** szerverbe:
```bash
vagrant ssh ansible
```

Telepítsd a távoli repository-t:
```bash
/shared/git.sh
```

Ezután futtasd az Ansible playbookot az alábbi paranccsal:
```bash
ansible-playbook -i /shared/inventory /shared/nginx_install.yml
```

---

## 4. Fájlstruktúra
A projekt főbb fájljai és mappái:
```
project_root/
├── Vagrantfile
├── shared/
│   ├── inventory
│   ├── nginx_install.yml
│   ├── shared/
├── templates/
│   ├── index.html.j2
└── README.md
```

---





