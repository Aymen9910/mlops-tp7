# Model Card - fraude (version servie par l'alias production)

## Modele
Nom et version dans le registre MLflow : fraude, version 1 (alias production)
Famille et reglages : RandomForestClassifier, n_estimators=500, max_depth=16 (scikit-learn 1.9.1, Python 3.14)
Run d'origine (MLflow) : b4207befd1ac4184a461496c90833fe5

## Objectif et usage prevu
Scorer chaque transaction de paiement (probabilite de fraude) pour signaler les transactions suspectes a un conseiller, qui decide du blocage.

## Usages exclus
Bloquer definitivement un compte ou refuser un client sans revue humaine. Scorer des transactions hors du perimetre des 4 variables (autre pays, autre devise, paiement professionnel).

## Donnees
Fichier, nombre de lignes, part de fraudes, empreinte (tag donnees_md5) : data/transactions.csv, 5 000 lignes, 11,36 % de fraudes, md5 cf9f6f86c141e7df32c61715c493483f
Origine des donnees (reelles ou synthetiques) : synthetiques (generees au TP2), 4 variables : montant, anciennete, freq_24h, nuit.

## Performances (jeu de test, 1 250 transactions)
F1 : 0.8669   precision : 0.8837   rappel : 0.8507   AUC : 0.9872
Matrice de confusion : [[1101, 15], [20, 114]] - 20 fraudes manquees sur 134, 15 fausses alertes.

## Methodes d'explicabilite
Globale : importance par permutation (perte de F1, 10 repetitions) et SHAP TreeExplainer (moyenne |SHAP| sur 400 transactions de test).
Locale : SHAP waterfall (exact, additif : base 0.116 + contributions = 0.924 pour Mme Benali) ; LIME (2 000 voisins, graine 0) en controle.
Variables les plus influentes : montant (effet positif au-dela d'environ 1 000 MAD), puis anciennete (faible anciennete = risque), freq_24h et nuit.

## Comparaison avec un modele interpretable
Une regression logistique sur log10(montant) atteint F1 = 0.8593 (113 fraudes detectees contre 114) : ecart d'une fraude, dans le bruit du jeu de test. Un arbre de profondeur 3 est lisible (3 regles) mais tombe a F1 = 0.5737.

## Limites et risques connus
Donnees synthetiques : performances non garanties sur des donnees reelles. SHAP decrit le modele, pas la causalite. LIME est instable : anciennete et freq_24h changent de signe selon la graine. La MDI sous-estime nuit (0.093 contre 0.196 en permutation). La variable nuit peut penaliser des clients au travail de nuit : effet a surveiller.

## Recommandations d'utilisation et de surveillance
Revue humaine obligatoire avant tout blocage ; explication SHAP fournie au conseiller. Surveiller chaque mois le F1, le rappel et la derive des distributions (montant, nuit). Reevaluer si l'alias production change de version.

## Tracabilite
Run des explications (experience tp7-explicabilite) : 29ca7fdeb227438cb9aa2d3dd4ce3757
Auteurs : NOMS DU GROUPE (5IASD, G6)   Date : 2026-11-12   Prochaine revue : 2027-02-12