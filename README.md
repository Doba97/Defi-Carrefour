# Défi IA 2024-2025 : Prédiction des réachats clients Carrefour

Projet réalisé dans le cadre du **Défi IA 2024-2025** organisé par **Carrefour (Analytics Factory)** en collaboration avec l'Université de Bordeaux.

**Auteurs** : Bah Amadou Ali, Kone Alabo Maria Yacine, Soro Doba Issiaka
**Établissement** : Université Félix Houphouët-Boigny

📄 Le rapport complet est disponible ici : [`RAPPORT_FINAL.pdf`](RAPPORT_FINAL.pdf)
📓 Le code est dans le notebook : [`projet_ufr.ipynb`](projet_ufr.ipynb)

---

## Résumé du projet

**Prédiction des achats clients, Challenge Data Carrefour et Université de Bordeaux** (octobre 2024 - janvier 2025)

🏆 **4ᵉ place** lors d'un challenge de Data Science organisé en partenariat entre Carrefour et l'Université de Bordeaux.

- Développement d'un système de recommandation visant à prédire les 10 produits les plus susceptibles d'être rachetés par chaque client, à partir des historiques d'achats 2022-2023
- Prétraitement des données, feature engineering et optimisation des modèles **XGBoost** et **LightGBM**
- Un HitRate@10 de l'ordre de **0.38** (0.3825 pour XGBoost, 0.3811 pour LightGBM), contre 0.33 pour la baseline, démontrant la capacité du modèle à recommander les produits les plus pertinents

## Énoncé du défi

L'objectif du défi était de développer un **système de recommandation** capable de prédire les **10 produits que chaque client est le plus susceptible d'acheter en 2024**.

**Données disponibles**

- Un historique de transactions de **100 000 clients Carrefour** sur 2022 et 2023 (clients, produits, dates d'achat, quantités, promotions, canaux d'achat)
- Des informations complémentaires sur les produits : secteur, catégorie, marque et diverses caractéristiques
- Les achats 2024 de **80 000 clients**, utilisés pour évaluer les prédictions
- **20 000 clients cibles** sans historique d'achat 2024 connu

**Tâche** : pour chacun des 20 000 clients cibles, produire une liste de 10 produits susceptibles d'être achetés en 2024, en s'appuyant sur leur comportement historique et sur les caractéristiques des produits. Les recommandations sont évaluées avec la métrique **Hitrate@10**.

**Difficulté principale** : la fréquence d'achat varie fortement selon les produits et selon les clients. Certains produits de consommation courante sont achetés très régulièrement, d'autres seulement occasionnellement. Le système doit donc prendre en compte les habitudes individuelles des clients et les caractéristiques des produits pour rester pertinent.

**Objectifs métier** : fidéliser les clients, augmenter les ventes en anticipant les besoins, et construire un modèle performant à partir des données historiques.

**Période du défi** : du 12 septembre 2024 au 9 janvier 2025.

## Métrique d'évaluation

**HitRate@10** : proportion des produits recommandés (parmi les 10 proposés) effectivement achetés par les clients.

- Score de la baseline fournie : **33 %** sur les données de validation
- Classement final basé sur un ensemble de test caché (les 20 000 derniers clients)

## Données

| Jeu de données | Contenu |
|---|---|
| `train_data` | Transactions de 100 000 clients sur 2022-2023 (10 fichiers de 10 000 clients) |
| `product_data.csv` | 82 966 produits avec leurs caractéristiques (bio, vegan, halal, marque, secteur, niveaux d'étagère, écoscore, etc.) |
| `test_data.csv` | Transactions 2024 des 80 000 premiers clients, utilisées pour la validation |

Les transactions 2024 des 20 000 derniers clients sont masquées : ce sont elles qu'il faut prédire.

**Variables des transactions** : `date`, `transaction_id`, `customer_id`, `product_id`, `has_loyality_card`, `store_id`, `is_promo`, `quantity`, `format` (clcv, lex, DRIVE), `orderChannelCode`.

**Qualité des données** : aucune donnée manquante dans les transactions. Dans `product_data`, des valeurs manquantes existent pour `brand_key`, `shelf_level3`, `shelf_level4` et `ecoscore`, et certaines colonnes sont constantes (`lactose_free`, `No_added_salts`, `Salt_reduced`, `Naturality`), donc sans intérêt.

## Méthodologie

1. **Exploration des données** : structure, valeurs manquantes, qualité
2. **Feature engineering** : création de variables sur les clients, les produits et les couples client-produit
3. **Modélisation** : comparaison de plusieurs algorithmes
4. **Évaluation** : AUC-ROC, précision, recall, F1-score, puis HitRate@10
5. **Recommandation** : sélection des 10 meilleurs produits par client

### Feature engineering

- **Caractéristiques de base** : nombre total de transactions, nombre de produits uniques achetés, taux d'achat en promotion
- **Caractéristiques client-produit** : nombre d'achats d'un produit, récence du dernier achat, proportion des quantités achetées
- **Saisonnalité** : proportion des achats effectués chaque mois

### Variable cible

Une variable binaire `target` indique si un produit a été acheté en 2024 par un client donné. Elle est obtenue par jointure sur `customer_id` et `product_id` entre l'historique 2022-2023 et les achats 2024 (`target = 1` pour un réachat, `0` sinon). Le problème devient une classification binaire.

## Résultats

| Approche | HitRate@10 |
|---|---|
| Baseline du défi | 0.33 |
| Fréquence des achats | 0.32 |
| Fréquence + récence + popularité globale | 0.35 |
| Régression logistique | performances limitées face aux modèles à base d'arbres |
| LightGBM | 0.3811 |
| **XGBoost (modèle retenu)** | **0.3825** (soumission) |

Le modèle XGBoost obtient un HitRate@10 de **0.3911** sur l'ensemble d'entraînement et **0.3825** en soumission, ce qui montre une légère perte de performance en phase de soumission. LightGBM, très proche, reste une alternative plus rapide à entraîner.

## Limites

- Ressources matérielles insuffisantes face à la taille des données
- Optimisation des hyperparamètres restreinte par le manque de puissance de calcul
- Gestion délicate de grands volumes de données dans le pipeline
- Organisation du travail en équipe à améliorer

## Perspectives

- Utiliser des solutions cloud (AWS, Google Cloud) et des GPU/TPU
- Mieux exploiter les ressources locales (mémoire, carte graphique)
- Approfondir les techniques d'optimisation et le calcul distribué
- Adopter une méthode de gestion de projet comme SCRUM

## Structure du dépôt

```
.
├── README.md
├── RAPPORT_FINAL.pdf     # Rapport complet du projet
└── projet_ufr.ipynb      # Notebook : traitement des données, modèles, prédictions
```

> Les jeux de données Carrefour (`train_data`, `product_data.csv`, `test_data.csv`) ne sont pas inclus dans ce dépôt.

## Technologies

Python, pandas, scikit-learn (régression logistique), XGBoost, LightGBM, Jupyter Notebook.
