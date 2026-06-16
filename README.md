# backups-S3
Le but de ce backup est de créer une automatisation pour envoyer tous nos documents depuis le repo que l'on veut directement dans un cloud (dans cet exemple j'ai utilisé le cloud d'Exoscale). Tout en utilisant les outils AWS et les sincroniser avec les outil S3.

## Outils a utiliser
1. PowerShell : le language de script de Windows qui va piloter le tout
2. AWS CLI (Command Line Interface) : C'est l'outil officiel en ligne de commande pour interagir avec le stockage S3. Même si on est chez une entreprise qui utilise une autre chose que le AWS, l'outil AWS fonctionne parfaitement car les entreprises cloud utilise la même norme (compatible avec S3)
3. Le planificateur de tâches windows (Task scheduler) : L'outil Windows pour lancer ton script automaticement toutes les heures.

# feuille de route
### Etape 1 : Préparer le terrain
 - Installe **AWS CLI** sur ta machine (il ya un installeur Windows Classique)
 - Configure le avec tes accès de ton organisation (Clé API / Secret Key et l'endpoint) en utilisant la commande aws configure sur le terminal powershell.

### Etape 2 : Le script PowerShell (`.ps1`)
Ton script devra faire trois chose simples :

#### 1. **Définir les cibles** : Créer une liste (*un tableau*) contenant les chemins des dossier locaux à sauvegarder (indiqués dans un fichier de conf)
#### **Exemple :**
`C:\Users\Ton_nom\Documents` 
**ou**
`C:\Users\Ton_nom\Pictures`

#### 2. **Boucler la liste** : Pour chaque dossier de la liste, lancer la commande de synchronisation
#### **Exemple :**
`aws s3 sync "Dossier" "s3://ton-bucket" --endpoint-url https://sos-ch-gva-2.exo.io`

#### 3. **La commande magique** : Utilise la commande `aws s3 sync`. Elle fonctionne exactement comme un `rsync` : elle compare le local et le cloud, et ne transfère **que ce qui a changé ou qui est nouveau**. C'est super rapide et robuste.

### Indice pour le code :
- Pour faire une boucle en PowerShell, regarde du côté de `Foreach ($dossier in $liste)`.
- La commande ressemblera a quelque chose comme sa : 
`aws s3 sync "C://nomdudossier" "s3://nombucket/sauvegarde" https://sos-ch-gva-2.exo.io` (il faudra la bonne URL)

### Etape 3 : Rendre le script robuste (Bonus)
pour que le script soit propre:
- Ajoute un system de "log": il faut faire en sorte que le script écrive dans un simple fichier texte `.txt` la date et l'heure de chaque exécution, et si tout s'est bien bien passer vous pouvez donc passé a l'étape suivante.

### Etape 4 : L'automatisation
Une fois que ton script fonctionne parfaitement quand tu le lance manuellement:
- Ouvre le **Plannificateur de tâches Windows**.
- Crée une nouvelle tâche qui exécute ton script Powerhell.
- Règle le déclencheur pour quàil se répète **toutes les heure, indéfiniment**.
># [!NOTE] En cas de bloquage vous pouvez vous aidé des code mis a disposition.
