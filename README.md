# 📊 BoutiqueParis Dashboard — Power BI Retail Analytics

> **Dashboard pédagogique Power BI** · Analyse des ventes d'une chaîne retail multi-magasins · Projet conçu pour former des étudiants Data à la construction d'un dashboard de pilotage commercial end-to-end.

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-003E54?style=for-the-badge)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Excel](https://img.shields.io/badge/Excel-217346?style=for-the-badge&logo=microsoftexcel&logoColor=white)

---

##   Contexte

Projet pédagogique conçu pour former des étudiants Data à la construction d'un **dashboard de pilotage commercial end-to-end** sur un cas retail multi-magasins.

**Entreprise fictive** : BoutiqueParis Fashion — chaîne de prêt-à-porter avec 8 points de vente en Île-de-France et région, sur 2 années d'activité (2024-2025).

---

## 📊 Dashboard livré

Dashboard interactif **3 pages** construit avec Power BI Desktop :

### Page 1 — Vue d'ensemble
- 5 KPI cards : CA Total, Marge, Taux de marge, Nombre de ventes, Panier moyen
- Évolution mensuelle du CA (saisonnalité)
- Top 10 Produits
- Répartition par Segment Client (Nouveau · Récurrent · VIP)
- Canaux d'acquisition

### Page 2 — Analyse Magasins
- Classement des 8 magasins par CA avec ranking RANKX
- Comparaison Boutiques vs Outlets (CA + Marge)
- Évolution mensuelle par type de magasin
- Slicers interactifs (type, ville)

### Page 3 — Produits & Clients
- Donut CA par Catégorie (Femme · Homme · Enfant)
- Top sous-catégories
- Tableau détaillé par segment (CA, nb clients, panier moyen)
- Répartition par canal d'acquisition

---

## 🏗️ Architecture technique

**Modèle en étoile** avec 1 table de faits + 4 dimensions :

```
                   +-------------+
                   |  Calendrier |
                   +------+------+
                          |
+-----------+        +----v------+        +-----------+
| Produits  |-------<|   VENTES  |>-------| Clients   |
+-----------+        +----+------+        +-----------+
                          |
                   +------v------+
                   |  Magasins   |
                   +-------------+
```

**Tables** :
- `Ventes` (fait) : 14 610 lignes
- `Produits` (dim) : 92 références
- `Magasins` (dim) : 8 points de vente
- `Clients` (dim) : 800 clients
- `Calendrier` (dim) : 731 jours (2024-2025)

---

## 📐 Mesures DAX implémentées

Le modèle intègre **11 mesures DAX** du basique à l'avancé :

### Mesures de base
```dax
Nb_Ventes = COUNTROWS ( Ventes )

CA_Total = 
SUMX ( Ventes,
    Ventes[quantite] * RELATED ( Produits[prix_unitaire] ) * ( 1 - Ventes[remise_pct] )
)

Marge = [CA_Total] - [Cout_Total]

Taux_Marge = DIVIDE ( [Marge], [CA_Total], 0 )

Panier_Moyen = DIVIDE ( [CA_Total], [Nb_Ventes], 0 )
```

### Mesures Time Intelligence
```dax
CA_YTD = TOTALYTD ( [CA_Total], Calendrier[date] )

CA_N_1 = CALCULATE ( [CA_Total], SAMEPERIODLASTYEAR ( Calendrier[date] ) )

Evolution_YoY = DIVIDE ( [CA_Total] - [CA_N_1], [CA_N_1], 0 )

CA_3M = 
CALCULATE (
    [CA_Total],
    DATESINPERIOD ( Calendrier[date], MAX ( Calendrier[date] ), -3, MONTH )
)
```

### Mesure avancée (classement)
```dax
Rank_Magasin = 
RANKX ( ALL ( Magasins ), [CA_Total], , DESC, DENSE )
```

---

##   Insights business identifiés

En explorant le dashboard, 5 insights métier émergent :

1. **Saisonnalité marquée** : Décembre génère ~2× le CA moyen mensuel (effet fêtes)
2. **Marge Boutiques (54%) > Marge Outlets (48%)** → effet remises systématiques en outlet
3. **Clients VIP** : panier moyen 1,5× supérieur aux Nouveaux
4. **Catégorie Homme dominante** (46,5% du CA) — contre-intuitif pour du retail mode
5. **Mix canaux équilibré** (18-22% chacun) → résilience marketing

---

## 📁 Structure du repo

```
boutiqueparis-dashboard/
├── README.md                           # ce fichier
├── dataset/
│   └── Projet_Ventes_BoutiqueParis.xlsx  # dataset source (5 tables)
├── scripts/
│   └── generate_dataset.py             # script Python de génération
├── dashboard/
│   └── Dashboard_BoutiqueParis.pdf     # export PDF du dashboard
├── docs/
│   ├── enonce_projet.pdf               # énoncé pédagogique
│   └── correction.pdf                  # correction avec DAX commenté
└── LICENSE                             # MIT
```

---

##   Comment reproduire le projet

### Pré-requis
- Power BI Desktop (gratuit) · [Télécharger](https://powerbi.microsoft.com/desktop)
- Python 3.9+ (pour regénérer le dataset si besoin)

### Installation

1. Cloner le repo :
```bash
git clone https://github.com/laurent-nzwenja/boutiqueparis-dashboard.git
cd boutiqueparis-dashboard
```

2. (Optionnel) Regénérer le dataset :
```bash
pip install pandas openpyxl numpy
python scripts/generate_dataset.py
```

3. Ouvrir Power BI Desktop → importer `dataset/Projet_Ventes_BoutiqueParis.xlsx`

4. Suivre les 7 étapes de construction dans `docs/correction.pdf`

---

## 🎓 Usage pédagogique

Ce projet est conçu pour :
- **Formateurs Data** : support de cours Power BI (10-15h de contenu)
- **Étudiants Data** : cas pratique pour maîtriser le modèle en étoile, DAX, Time Intelligence
- **Recruteurs** : démonstration de compétences BI end-to-end

**Objectifs d'apprentissage** :
- Importer et transformer des données avec Power Query
- Construire un modèle en étoile et marquer la table de dates
- Écrire des mesures DAX (CALCULATE, Time Intelligence, RANKX)
- Créer des visuels interactifs avec slicers
- Publier et partager un rapport sur Power BI Service

---

## 👨‍💻 Auteur

**Laurent NZWENJA DJIAM**
Data Analyst · Analytics Engineer · Consultant Power BI
3+ ans d'expérience · Ingénieur ECE Paris · MSc Data Engineer & IA

- 🔗 [LinkedIn](https://linkedin.com/in/laurent-nzwenja-djiam)
- 🌐 [Portfolio Notion](VOTRE_URL_NOTION)
- 📧 laurentdjiam@gmail.com

---

##   Licence

Ce projet est sous licence **MIT** — vous êtes libre de l'utiliser, modifier et distribuer pour vos propres besoins pédagogiques ou professionnels.

Voir le fichier [LICENSE](LICENSE) pour les détails.

---

##   Si ce projet vous est utile

Pensez à **  star le repo** pour le partager avec la communauté Data francophone !

---

*Projet construit en octobre 2026 · Dernière mise à jour : 2026-10-19*
