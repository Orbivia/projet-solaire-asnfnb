SolaExpert ASNBNF - Calculateur Solaire Autonome

Application web légère et autonome conçue pour dimensionner des installations solaires photovoltaïques en site isolé (off-grid).

📱 Aperçu de l'Application

L'application est conçue pour être utilisée de manière fluide sur smartphone (mobile-first) comme sur ordinateur de bureau. Elle accompagne l'installateur à travers 4 étapes clés :

1. 📍 Localisation & Ensoleillement

API PVGIS : Récupération automatique des données d'ensoleillement en cliquant sur une carte interactive.

Mode Pédagogique (Facteur F) : L'utilisateur peut laisser le réglage sur "Optimisé" (Facteur de 1.00) ou régler manuellement l'inclinaison et l'orientation pour observer l'impact d'une mauvaise exposition sur la baisse du rendement.

2. 💡 Bilan de Consommation

Saisie robuste des équipements : définition de la quantité, des heures d'utilisation et du nombre de jours par semaine.

Sécurité des saisies : Alertes si l'utilisation dépasse 24h ou si les heures de nuit dépassent les heures totales.

Consommation de Nuit : Le système isole spécifiquement l'énergie consommée la nuit pour dimensionner précisément le parc de batteries.

3. ⚙️ Paramètres Techniques

Ajustement fin des rendements (onduleur, câbles, chaleur).

Technologie de Batterie : Un menu déroulant simple (Lithium, Plomb Gel, Plomb Ouvert) définit automatiquement la Profondeur de Décharge (DoD) sans que l'utilisateur n'ait à connaître les ratios par cœur.

Choix de l'architecture de stockage (12V, 24V, 48V).

4. 📊 Synthèse et Dimensionnement

Recommandations d'achats : Affiche le besoin exact et la recommandation d'achat (ex: arrondissement du nombre de panneaux au chiffre pair supérieur pour s'adapter aux régulateurs MPPT).

Schéma unifilaire : Génération dynamique du schéma de câblage (Panneaux en série/parallèle, Régulateur, Batteries en série/parallèle) en fonction des choix techniques précédents.

🛠️ Architecture Technique

SolaExpert repose sur une architecture minimaliste pour garantir une maintenance facile :

Front-end (Client) :

HTML5, CSS3 (Vanilla, sans framework lourd).

JavaScript (ES6) pour la logique de calcul en temps réel et la génération du schéma SVG.

Bibliothèques : Leaflet.js (Carte), Chart.js (Graphique).

Back-end (Serveur) :

Python 3 / Flask (Micro-framework web).

Requests (Pour interroger l'API européenne PVGIS en contournant les problèmes de CORS navigateur).

🧮 Méthodologie et Formules de Calcul

Le calculateur utilise des formules standards de l'industrie photovoltaïque pour estimer les besoins :

1. Bilan Énergétique Journalier ($E_j$)

Le besoin total est calculé en moyennant l'utilisation sur la semaine :

$$E_j (Wh/j) = \sum \left( P \times Qté \times H \times \left(\frac{Jours/semaine}{7}\right) \right)$$

2. Énergie à Produire ($E_p$)

On prend en compte l'ensemble des pertes du système (Rendement Global).

$$E_p = \frac{E_j}{R_{global}}$$

Où $R_{global}$ = $R_{onduleur} \times R_{régulateur} \times R_{batteries} \times R_{cables} \times R_{chaleur} \times R_{inclinaison\_orientation}$ (Le Facteur F).

3. Dimensionnement du Parc Solaire

La Puissance Crête ($P_c$) nécessaire est calculée selon l'irradiation locale de référence ($Ir$).

$$P_c (W_c) = \left( \frac{E_p}{Ir} \right) \times 1000$$

Le nombre de panneaux est calculé, puis arrondi au nombre pair supérieur.

$$Nb_{panneaux\_exact} = \frac{P_c}{P_{unitaire\_panneau}}$$

4. Dimensionnement du Parc Batterie (Stockage)

C'est ici qu'intervient la notion de Consommation de nuit. Le système doit stocker assez d'énergie pour la nuit $E_{nuit}$ OU pour survivre à plusieurs jours sans soleil ($E_j \times J_{auto}$). L'algorithme prend le besoin le plus critique des deux.

$$E_{stock} = \max (E_{nuit}, E_j \times J_{auto})$$

Capacité théorique du parc ($C_{th}$ en Ah) en fonction de la Profondeur de Décharge (DoD) dictée par la technologie de batterie :

$$C_{th} = \frac{E_{stock}}{U_{sys} \times DoD \times R_{batteries}}$$

Une marge de sécurité de 20% (facteur 0.8) est ensuite appliquée pour prolonger la durée de vie du parc.

5. Section de Câblage (Chute de tension $\Delta V$)

Pour éviter les incendies et les pertes, la section ($S$ en $mm^2$) est calculée avec une tolérance de chute de tension de 3% max, puis arrondie à la section commerciale supérieure (ex: 4, 6, 10, 16 mm²).

$$S = \frac{\rho \times 2 \times L \times I}{\Delta V_{max}}$$
