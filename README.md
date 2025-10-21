# pzs-ng PreTimeSQL Plugin

## 📋 Description

PreTimeSQL est un plugin pzs-ng destiné à enregistrer et exploiter les informations de pretime (pré-sorties) dans une base SQL. Il remplace/complète les annonces NEWDIR en ajoutant le temps de pre et en historisant les releases pour consultation et analyse.

- Cadre: pzs-ng (glFTPd)
- Dépendance recommandée: MySQLManager (gestion MySQL centralisée)
- Objectif: historisation fiable, annonces thémables, requêtes performantes

## ✨ Fonctionnalités

- Capture des évènements NEWDIR et stockage SQL
- Création/MAJ automatique de la table au premier run (si implémentée)
- Ajout automatique des releases manquantes avec horodatage pre courant (option)
- Mise à jour des timestamps NULL lors d’un NEWDIR (option)
- Stockage enrichi optionnel: section, site, uploader, groupe, évènement
- Annonces totalement thémables via `PreTimeSQL.zpt`
- Liste d’exclusions de répertoires (ex: sample, subs)

## 📦 Prérequis

- pzs-ng en place et fonctionnel
- Tcl 8.6+
- Plugin MySQLManager chargé avant PreTimeSQL (recommandé)
- Accès à une base MySQL/MariaDB (ou autre selon implémentation)

## 🚀 Installation

1) Placer les fichiers
- `PreTimeSQL.tcl`
- `PreTimeSQL.zpt`
Dans `scripts/pzs-ng/plugins/` de votre bot.

2) eggdrop.conf (ordre critique)
```tcl
# Gestionnaire de connexion DB
source pzs-ng/plugins/MySQLManager.tcl
# Annonceur PreTime
source pzs-ng/plugins/PreTimeSQL.tcl
```

3) Configurer
Éditez les variables en tête de `PreTimeSQL.tcl` (connexion, options d’auto-add/update, exclusions, thème).

4) Rehash/redémarrage
Rehash Eggdrop. La table pourra être créée automatiquement si absente selon l’implémentation.

## ⚙️ Configuration (extraits)

- Connexion: nom de connexion MySQLManager, hôte, base, user, pass
- Comportement: auto-add, update timestamps NULL, exclusions
- Thème: variables exposées dans `PreTimeSQL.zpt`

Schéma minimal d’exemple:
```sql
CREATE TABLE IF NOT EXISTS pretime (
  id INT AUTO_INCREMENT PRIMARY KEY,
  release VARCHAR(255) NOT NULL,
  section VARCHAR(64),
  group_name VARCHAR(128),
  site VARCHAR(128),
  uploader VARCHAR(128),
  pre_timestamp TIMESTAMP NULL,
  event VARCHAR(32),
  extra JSON NULL,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  KEY idx_release (release),
  KEY idx_pre_timestamp (pre_timestamp)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

## 🔎 Utilisation

- Le plugin enrichit les annonces NEWDIR et journalise les releases
- Requêtes utiles:
  - Dernières 50 pre: `SELECT * FROM pretime ORDER BY pre_timestamp DESC NULLS LAST LIMIT 50;`
  - Par section: `SELECT * FROM pretime WHERE section='TV' ORDER BY pre_timestamp DESC;`

## 🧪 Dépannage

- Vérifier l’ordre de chargement (MySQLManager avant PreTimeSQL)
- Vérifier la connectivité SQL et les droits
- Contrôler les logs pzs-ng en cas d’échec d’insertion ou de template

## 🤝 Contribution

- Fork → branche feature/xxx → PR
- Respect du style Tcl/pzs-ng et conventions du dépôt
- Ouvrir des issues avec logs, versions, étapes de repro

## 📝 Licence

MIT — voir LICENSE (ajoutez-le si manquant).

## 👤 Auteur

ZarTek-Creole — https://github.com/ZarTek-Creole
