# RAID
# Création d'un RAID 1

## Configuration de la machine de test

- **OS** : Linux Ubuntu 22.04 LTS
- **Disque 1** : pour le système
- **3 disques supplémentaires** de 5 Go chacun pour les manipulations sur le RAID (la taille doit être identique pour tous les disques).

## Partitionnement des disques

### Vérification des disques

- Utilisez la commande suivante pour voir les 3 disques disponibles pour le RAID :

```bash
lsblk
```

### Partitionner le disque `/dev/sdb`

- Lancez l'utilitaire `fdisk` :

```bash
sudo fdisk /dev/sdb
```

- Dans l'interface `fdisk` :
  - Tapez `m` pour afficher l'aide.
  - Ajoutez une nouvelle partition avec `n`.
  - Créez une partition primaire qui prend toute la taille du disque.
  - Changez le type de partition en RAID Linux :
    - Tapez `t`.
    - Entrez le code hexadécimal `fd` pour RAID Linux.
  - Sauvegardez et quittez en tapant `w`.

- Vérifiez la partition créée :

```bash
sudo fdisk -l
```

### Répéter l'opération pour `/dev/sdc`

- Exécutez les mêmes commandes pour partitionner `/dev/sdc`.

## Installation de `mdadm`

- Si `mdadm` n'est pas installé, exécutez :

```bash
sudo apt install mdadm
```

## Création du RAID 1

- Exécutez la commande suivante pour créer le RAID :

```bash
sudo mdadm --create /dev/md0 --level 1 --raid-devices 2 /dev/sdb1 /dev/sdc1
```

## Vérification du RAID

- Pour vérifier l'état du RAID :

```bash
cat /proc/mdstat
```

- Ou avec `mdadm` :

```bash
sudo mdadm --detail /dev/md0
```

- Afficher les disques inclus dans le RAID :

```bash
lsblk -f
```

## Formatage du RAID

- Formatez le volume RAID en `ext4` avec le nom *PersonalData* :

```bash
sudo mkfs.ext4 /dev/md0 -L "PersonalData"
```

- Vérifiez le formatage :

```bash
lsblk -f
```

## Montage du RAID

- Créez un dossier de montage :

```bash
sudo mkdir -p /home/wilder/Data-RAID1
```

- Montez le RAID dans ce dossier :

```bash
sudo mount /dev/md0 /home/wilder/Data-RAID1/
```

### Montage automatique au démarrage

- Ajoutez la ligne suivante à `/etc/fstab` :

```bash
/dev/md0 /home/wilder/Data-RAID1 ext4 nofail 0 0
```

## Verrouillage du nom `md0`

- Exécutez la commande suivante pour verrouiller le nom du RAID :

```bash
sudo mdadm --detail --scan >> /etc/mdadm/mdadm.conf
sudo update-initramfs -u
```

## Test du bon fonctionnement du RAID

- Créez un fichier et un dossier dans le RAID :

```bash
touch /home/wilder/Data-RAID1/test_raid1.txt
mkdir /home/wilder/Data-RAID1/dossier_test
```

- Vérifiez leur présence :

```bash
ls -l /home/wilder/Data-RAID1
```

## Conclusion

- Votre RAID 1 est maintenant configuré et fonctionnel sur votre machine Ubuntu 22.04 LTS.
