# DIZEUR — signature de la version Release

La version Debug ne doit pas être distribuée comme version définitive. La version Release est signée avec une clé privée permanente appartenant au propriétaire du projet.

## 1. Créer la clé privée (sur un ordinateur avec Java installé)

Dans un terminal, exécuter :

```bash
keytool -genkeypair -v -keystore dizeur-release.jks -keyalg RSA -keysize 3072 -validity 10000 -alias dizeur
```

Conserver le fichier `dizeur-release.jks` et les mots de passe dans un endroit sûr. Ne jamais les publier dans le dépôt GitHub ni les envoyer dans une conversation.

## 2. Encoder le fichier

Linux/macOS :
```bash
base64 -w 0 dizeur-release.jks
```

macOS si `-w` n'est pas reconnu :
```bash
base64 dizeur-release.jks | tr -d '\\n'
```

Windows PowerShell :
```powershell
[Convert]::ToBase64String([IO.File]::ReadAllBytes("dizeur-release.jks"))
```

Copier le résultat complet.

## 3. Ajouter les secrets GitHub

Dans le dépôt GitHub, ouvrir **Settings → Secrets and variables → Actions → New repository secret** et créer les quatre secrets suivants :

- `DIZEUR_KEYSTORE_BASE64` : contenu Base64 du fichier de clé.
- `DIZEUR_STORE_PASSWORD` : mot de passe du keystore.
- `DIZEUR_KEY_ALIAS` : `dizeur`.
- `DIZEUR_KEY_PASSWORD` : mot de passe de la clé.

Ne jamais inscrire les mots de passe dans le code ou dans un fichier du dépôt.

## 4. Construire la version signée

Ouvrir **Actions → Build DIZEUR signed release → Run workflow**. Le workflow vérifie la signature avant de publier l'APK comme artefact `DIZEUR-release-signed`.

## Important

- Sauvegarder la clé et ses mots de passe : sans eux, il ne sera pas possible de signer les futures mises à jour avec la même identité.
- Une signature Release valide ne garantit pas que Play Protect acceptera l'application. Le contrôle Play Protect est indépendant de la compilation et de la signature.
- L'APK doit encore être testé sur le téléphone réel avant de considérer la version comme validée.
