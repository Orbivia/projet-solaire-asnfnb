SolaExpert ASNBNF - Calculateur Solaire Autonome

Application web légère et autonome conçue pour dimensionner des installations solaires photovoltaïques en site isolé (off-grid).


📱 Aperçu de l'Application

L'application est conçue pour être utilisée de manière fluide sur smartphone comme sur ordinateur de bureau. Elle se divise en quatre étapes clés :

📍 Localisation & Ensoleillement : Permet à l'utilisateur de cliquer sur une carte interactive pour récupérer les données précises d'ensoleillement (via l'API PVGIS). Elle inclut un mode pédagogique pour comprendre l'impact d'une mauvaise orientation/inclinaison des panneaux.

💡 Bilan de Consommation : Un module de saisie des équipements électriques (types d'appareils, puissance, nombre d'heures de fonctionnement jour/nuit) pour estimer le besoin énergétique global.

⚙️ Paramètres Techniques : Une section détaillée permettant aux installateurs d'ajuster les rendements (onduleur, batteries, pertes câbles), les caractéristiques des panneaux choisis, et la tension du système de stockage (12V, 24V, 48V).

📊 Synthèse et Dimensionnement : Le tableau de bord final génère automatiquement les recommandations pour l'achat du matériel (nombre de panneaux en configuration paire, capacité du parc batterie, sections de câbles minimales) et dessine un schéma unifilaire dynamique du câblage à réaliser.

🛠️ Architecture Technique

SolaExpert repose sur une architecture :

Front-end (Client) :

HTML5, CSS3 (Vanilla, sans framework lourd)

JavaScript (ES6) pour la logique de calcul en temps réel et la génération du schéma SVG.

Bibliothèques externes :

Leaflet.js (Cartographie interactive).

Chart.js (Graphique d'ensoleillement mensuel).

Back-end (Serveur) :

Python 3

Flask (Micro-framework web)

Requests (Pour interroger l'API européenne PVGIS en contournant les problèmes de CORS).

🧮 Méthodologie et Formules de Calcul

Le calculateur utilise des formules standards de l'industrie photovoltaïque pour estimer les besoins. Voici les principales logiques appliquées :

1. Bilan Énergétique Journalier ($E_j$)

Le besoin total est calculé en multipliant la puissance de chaque appareil par son temps d'utilisation, moyenné sur la semaine.

$$E_j (Wh/j) = \sum \left( P \times Qté \times H \times \left(\frac{Jours/semaine}{7}\right) \right)$$

2. Énergie à Produire ($E_p$)

On prend en compte l'ensemble des pertes du système pour savoir combien d'énergie les panneaux doivent réellement générer.

$$E_p = \frac{E_j}{R_{global}}$$

Où $R_{global}$ = $R_{onduleur} \times R_{régulateur} \times R_{batteries} \times R_{cables} \times R_{chaleur} \times R_{inclinaison\_orientation}$.

(Le facteur $R_{inclinaison\_orientation}$ ou Facteur F est ajusté dynamiquement si l'utilisateur désactive le mode d'orientation "Optimisé").

3. Dimensionnement du Parc Solaire

On calcule d'abord la Puissance Crête ($P_c$) nécessaire en fonction de l'irradiation locale de référence ($Ir$) récupérée via PVGIS.

$$P_c (W_c) = \left( \frac{E_p}{Ir} \right) \times 1000$$

Le nombre exact de panneaux est calculé, puis arrondi au nombre pair supérieur pour respecter les bonnes pratiques de câblage sur les régulateurs MPPT modernes.

$$Nb_{panneaux\_exact} = \lceil \frac{P_c}{P_{unitaire\_panneau}} \rceil$$

4. Dimensionnement du Parc Batterie (Stockage)

La capacité de la batterie doit couvrir le besoin le plus exigeant entre :
a) Fournir l'énergie pendant la nuit ($E_{nuit}$).
b) Garantir l'autonomie ($J_{auto}$) lors des jours sans soleil.

Énergie totale à stocker ($E_{stock}$) :

$$E_{stock} = \max (E_{nuit}, E_j \times J_{auto})$$

Capacité théorique du parc ($C_{th}$ en Ah) :

$$C_{th} = \frac{E_{stock}}{U_{sys} \times Prof_{decharge} \times R_{batteries}}$$

(Où $U_{sys}$ est la tension du système, ex: 12V, 24V ou 48V).

Capacité Réelle recommandée (avec marge de sécurité contre le vieillissement prématuré, facteur de 0.8) :

$$C_{reelle} = \frac{C_{th}}{0.8}$$

5. Section de Câblage (Méthode de la chute de tension $\Delta V$)

Pour éviter les échauffements et les pertes, la section ($S$ en $mm^2$) est calculée avec une tolérance de chute de tension de 3% max.

$$S = \frac{\rho \times 2 \times L \times I}{\Delta V_{max}}$$

(Où $\rho = 0.017 \Omega.mm^2/m$ pour le cuivre, $L$ la distance, $I$ l'intensité maximale transitant dans le câble).
L'application sélectionne ensuite automatiquement la section commerciale standard directement supérieure (ex: si le calcul donne $7 mm^2$, l'application recommandera du $10 mm^2$).
.
