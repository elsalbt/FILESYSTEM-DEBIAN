# FILESYSTEM-DEBIAN
Atelier FILESYSTEM sur Debian 

# ATELIER FILESYSTEM SUR DEBIAN

## VOIR RECO MACHINE

---

### CONFIGURATION
- **AJOUTER UN DEUXIEME DISQUE AVANT TOUT (8GO C'EST OK)**
- **LA METTRE EN PONT**

---

### EXERCICE
- **ON VEUT PARTITIONNER LE DEUXIEME DISQUE : UNE SVG ET UNE SWAP**
- **SE CONNECTER EN ROOT**
  - `blkid` comme `lsblk -f` pour UUID.

---

### ETAPES

1. **VÉRIFIER PARTITIONNEMENT AVEC `lsblk`:**
   - On voit que le disque principal est en 3 partitions dont une déjà SWAP => système.
   - On voit le `sdb` non partitionné.

2. **Partitionner avec `cfdisk /dev/sdb`:**
   - Choisir `DOS = MBR`.
   - Partitionner en deux avec "Entrer" :
     - Une de 6 Go en *primary*.
     - La seconde avec le reste en *primary* également.
   - "Write" puis quitter.

3. **Vérifier avec `lsblk` que tout est OK.**

4. **Formater les deux partitions :**
   - `mkfs.ext4 -L BACKUP /dev/sdb1` => Renommer avec `-L` (UUID généré automatiquement).
   - `mkswap -L SWAP /dev/sdb2` => Partitionner en swap et renommer.

5. **Vérifier avec `lsblk -f` pour les UUID.**

6. **Désactiver le `sda5` en SWAP et passer sur le nouveau :**
   - `swapoff /dev/sda5` => Vérifier la désactivation du SWAP sur `sda5`.
   - `swapon /dev/sdb2` => Vérifier que `sdb2` est bien SWAP.

7. **Créer un dossier de montage pour le backup, puis monter :**
   - `ls /mnt` => Pour voir les points de montage.
   - `mkdir /mnt/backup`.
   - `mount /dev/sdb1 /mnt/backup`.

8. **Vérifier avec `lsblk` que le disque est monté dans `/backup`.**

9. **Configurer les points de montage au démarrage dans `/etc/fstab` :**
   - **Vérifier le contenu avec `cat`:**
     - Récupérer l'UUID avec `blkid | grep LABEL >> /etc/fstab`.
     - En SSH (`root@adresseip`) sur deux fenêtres Windows (revoir SSH) :
       - Sur la première fenêtre : `lsblk -f`.
       - Sur la seconde : `nano /etc/fstab`.
     - Ajouter un nouvel argument ou copier/coller une ligne du fichier en remplaçant :
       - UUID pour `sdb1` et `sdb2` :
         ```txt
         ext4  default  0  0
         swap  sw       0  0
         ```
     - Désactiver celui de `sda`.

10. **Vérifier avec :**
    - `mount -a`.
    - `lsblk` que tout est OK.
    - Redémarrer avec `reboot`.
    - Vérifier `/etc/fstab`.
