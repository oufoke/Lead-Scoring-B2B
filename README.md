# Lead Scoring — Savoir quels prospects appeler en premier

> 🇫🇷 Un moteur de scoring qui priorise les prospects et explique chaque score. Entraîné sur données synthétiques.
> 🇬🇧 A lead scoring engine that prioritises prospects and explains every score. Trained on synthetic data.

**[Lancer la démo](https://lead-scoring-portofolio-ofk.streamlit.app/)**

---

## Le problème

Une équipe commerciale qui traite tous ses prospects à égalité dilue son effort. Le coût d'acquisition monte, et les affaires les plus probables reçoivent la même attention que les moins probables.

Un score de conversion permet de trier. Mais un score seul ne fait pas agir : un commercial n'appelle pas parce qu'on lui affiche 0,82. Il appelle quand il sait **pourquoi**.

---

## Ce que fait le système

Un score de probabilité de conversion par prospect, accompagné de la contribution de chaque variable à ce score — positive ou négative, chiffrée.

### Le mécanisme

Le modèle construit des arbres de décision en série. Chaque nouvel arbre ne prédit pas la cible : il prédit l'erreur laissée par les arbres précédents. En additionnant, on corrige progressivement. C'est ce qui rend la méthode performante sur des données en tableau, là où un réseau de neurones n'apporte rien.

L'explication repose sur une idée de théorie des jeux : on mesure ce que chaque variable apporte à la prédiction en comparant les résultats obtenus avec et sans elle. Chaque variable reçoit une part attribuée, qui explique l'écart entre la prédiction et la moyenne.

---

## Stack

* **Langage** — Python
* **Modèle** — XGBoost
* **Explication** — SHAP
* **Traitement** — Pandas, NumPy
* **Interface** — Streamlit

---

## Décisions & arbitrages

*Section rétrospective.*

### Arbres boostés plutôt qu'un réseau de neurones

**Contexte.** Données tabulaires, volume modeste, besoin d'explication.
**Décision.** XGBoost.
**Pourquoi.** Sur ce type de données, les méthodes d'arbres boostés restent au niveau des meilleures approches pour une fraction du coût et du temps de mise au point. Un réseau de neurones aurait ajouté de l'opacité sans gain.

### L'explication comme fonctionnalité produit, pas comme option

**Contexte.** Le destinataire est un commercial, pas un data scientist.
**Décision.** L'attribution par variable est affichée systématiquement, pas cachée derrière un onglet expert.
**Pourquoi.** C'est ce qui transforme un score en action. Sans l'explication, l'outil n'est pas adopté — le score reste un chiffre qu'on ignore.

### Données synthétiques

**Contexte.** Pas d'accès à un pipeline commercial réel.
**Décision.** Générer un jeu de données simulant un cycle de vente B2B.
**Ce que ça coûte.** C'est la limite principale du projet, détaillée plus bas. Le projet démontre une chaîne de bout en bout, pas une capacité de prédiction.

---

## Limites connues

### La limite principale : les données sont synthétiques

Les métriques de ce dépôt ne mesurent pas une capacité de prédiction sur le monde réel. Elles mesurent la capacité du modèle à **retrouver la structure que le générateur de données a lui-même écrite**. Le raisonnement est circulaire et le chiffre n'est pas transposable.

Ce que le projet démontre : la chaîne complète — préparation, entraînement, explication, déploiement.
Ce qu'il ne démontre pas : une performance prédictive.

### Le taux de bonnes réponses est une métrique trompeuse ici

Sur un problème où la classe positive est rare, un modèle qui prédit « non » partout affiche un excellent score global. Les métriques pertinentes seraient la précision sur les premiers prospects classés et le gain par rapport à un tri aléatoire.

### Les pièges d'une mise en production réelle

* **Fuite de données.** Si une variable n'existe qu'après la signature, le modèle la lit et affiche une performance parfaite qui ne vaut rien en production. Un score trop beau en validation est presque toujours le signe qu'on a laissé passer le futur.
* **Scores non calibrés.** 0,8 ne signifie pas 80 % de chances de signer. Sans calibration, impossible de raisonner en espérance de revenu.
* **Boucle de rétroaction.** Si les commerciaux ne traitent que les prospects bien notés, seuls ceux-là produisent un résultat observable. Le modèle réentraîné se confirme lui-même et enterre les segments qu'il avait sous-évalués.
* **Dérive.** Un changement de marché ou de canal d'acquisition rend le modèle obsolète sans qu'aucune alerte ne se déclenche.

---

## Ce qui n'a pas été mesuré

Performance sur données réelles, calibration des scores, stabilité dans le temps — et la seule chose qui compte vraiment : est-ce que les commerciaux qui suivent le score signent plus que ceux qui l'ignorent ?

---

## Difficultés rencontrées

* **Éviter que le générateur de données ne rende le problème trop facile.** Un jeu synthétique mal conçu produit un modèle parfait et inutile. Calibrer le bruit et les corrélations pour que l'exercice reste réaliste a demandé plusieurs itérations.


---

*Oumar Fodé KEBE — [oufoke.github.io](https://oufoke.github.io) · [LinkedIn](https://www.linkedin.com/in/oumarfodek/)*
