# DD

## DEFINITION

> Cette commande permet de copier _TOUT_ ou _PARTIE_ d'un disque par bloc d'octets, indépenamment de la structure, du contenu du disque.

## STRUCTURE DE LA COMMANDE

  `dd if=<source> of=<cible> bs=<tailles des blocs> skip=  seek=  conv=<conversion>``

* **source**:     donnees à copier
* **cible**:      endroit où les copier
* **if**:         input file
* **of**:         output file
 * **bs**:         (block size) est une puissance de z>= 512 reprsentant 1 nb d'octets (ex: 512; 1024; 2048...)
* **skip**:       saute des blocs d'entrée au début d'un media
* **seek**:       saute des blocs sur le media de sortie avant d'écrire
* **conv**:       **notrunc**; **noerror** (noerror est riqué car continu même s'il y a des erreurs)

        ex:     dd if=/dev/sda of=/dev/sdb bs=1M
