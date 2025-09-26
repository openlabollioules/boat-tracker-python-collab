# Projet de Surveillance Maritime (Version BÊTA)

Ce dépôt contient le code source d'une application interactive développée dans un environnement Google Colab, axée sur la surveillance maritime et l'analyse de données AIS (Automatic Identification System) en temps quasi réel.
Le projet est actuellement en **version BÊTA** et représente une base fonctionnelle pour des développements futurs.

## Vue d'ensemble

L'objectif de ce projet est de créer un outil intelligent capable d'ingérer des données AIS, de visualiser la situation maritime sur une carte, et de répondre à des questions de l'utilisateur sur des navires 
spécifiques en utilisant un assistant IA outillé.

## Stack Technique et Bibliothèques Utilisées

Le projet est construit en Python et utilise les bibliothèques principales suivantes :

*   **`asyncio`**, **`websockets`**, **`nest_asyncio`** : Pour la connexion et le traitement asynchrène des données AIS via un flux WebSocket.
*   **`pandas`** : Pour la manipulation et l'analyse structurée des données AIS reçues.
*   **`folium`** : Pour la création de cartes interactives et la visualisation des navires et autres couches géographiques (trafic, câbles).
*   **`ipywidgets`** : Pour construire l'interface utilisateur interactive dans le notebook (sélecteurs, boutons, chat).
*   **`langchain`** : Framework utilisé pour construire l'agent conversationnel outillé, permettant au chatbot de raisonner et d'utiliser des outils spécialisés pour répondre aux questions maritimes.
*   **`langchain_openai`** / **`langchain_core`** : Composants de LangChain pour l'intégration des modèles de langage et la gestion des requêtes.
*   **`numpy`** : Utilisé pour les calculs numériques, notamment dans l'analyse comportementale des navires.
*   **`geopy.distance`** (et fonction `haversine_m` implémentée) : Pour calculer les distances orthodromiques entre les points géographiques.
*   **`requests`** : Potentiellement utilisée pour interroger des APIs externes (météo, bathymétrie).
*   **`rasterio`** (mentionné mais non utilisé pour l'instant) : Prévu pour la gestion de données raster comme les GeoTIFFs.
*   **API OpenWeather** : Utilisée pour obtenir des données météorologiques (vent, etc.) dans la zone des navires analysés, enrichissant l'évaluation de risque.

## Fonctionnalités Actuelles (Version BÊTA)

Dans cette version BÊTA, l'application est capable de :

*   Se connecter à un flux de données AIS (AIStream).
*   Maintenir un état quasi temps réel des navires dans une zone géographique définie (Méditerranée).
*   Visualiser les navires sur une carte interactive avec des informations basiques (nom, MMSI, position, vitesse, cap, type, pavillon) dans des popups et un panneau latéral.
*   Afficher une couche de **densité de trafic par kilomètre carré**, permettant de visualiser directement les zones à forte concentration de navires et d'identifier visuellement
    les navires potentiellement isolés ou dans des zones anormales.
*   Offrir une interface de chat où l'utilisateur peut poser des questions en langage naturel sur les navires visibles.
*   Utiliser un agent IA outillé pour répondre à une série de questions maritimes en interrogeant les données AIS disponibles et des sources externes basiques (météo, bathymétrie approximative).

Le chatbot peut actuellement répondre à des questions telles que :

*   "Dans quelle zone est le navire XXX ?"
*   "Où se situe le navire XX ?"
*   "À quel pays appartient le navire XX ?"
*   "Quel est le nom du navire qui possède ce MMSI [MMSI] ?"
*   "Quelles sont les conditions météo dans la zone où se situe ce navire ?"
*   "Y a-t-il des navires proches de ce navire ? "
*   "Ce navire est-il dans une zone portuaire ?" (Basé sur heuristique de profondeur)
*   "Ce navire est-il dans une zone maritime internationale ?" (Basé sur heuristique de profondeur/distance côte)
*   "Ce navire est-il isolé ?" (Basé sur la détection de voisins proches)
*   "Donne-moi les trois navires les plus proches de celui ci et leur distance."
*   "Le navire a il une trajectoire suspecte ?"
*   "De quel type est ce navire ? "
*   Analyser certains comportements comme le loitering ou l'arrêt prolongé.
*   Identifier un possible transbordement (STS) basé sur des critères de proximité et de vitesse basse prolongée.

## Amélioration Continue et Feedback Human-in-the-Loop

Pour encourager et maximiser l'amélioration continue du chatbot et de ses capacités d'analyse, deux systèmes de feedback Human-in-the-Loop ont été intégrés :

1.  **Feedback Manuel Structuré :** Après chaque réponse du chatbot, un panneau de feedback permet à l'utilisateur de noter la réponse (👍/👎) et de fournir des commentaires textuels.
    Ces retours sont collectés et peuvent être exportés (potentiellement via un fichier JSON après une certaine période d'utilisation). Cette approche permet d'identifier précisément les requêtes problématiques ou les domaines à améliorer de manière ciblée (correction de bugs, affinage des réponses, ajout de connaissances).
3.  **Apprentissage Automatique via HumanLLm :** Une bibliothèque nommée "humanLLm" a été intégrée. Après un certain nombre de requêtes (actuellement configuré pour être visible après 5 requêtes dans la conversation),
    un mécanisme interactif peut être déclenché. L'utilisateur peut alors valider ou corriger la réponse du chatbot. Ce feedback est utilisé en interne pour ajuster et enrichir automatiquement le prompt du chatbot, lui permettant d'apprendre et de s'améliorer continuellement au fil des interactions.

Ces méthodes de collaboration entre l'humain et le LLM visent à faire converger les performances du chatbot vers l'excellence opérationnelle en s'adaptant aux besoins réels et aux cas d'usage spécifiques.

## Prochaines Étapes et Développement Futur

Cette version BÊTA constitue un tremplin pour des capacités d'analyse et de surveillance beaucoup plus avancées. Pour les prochaines étapes, nous prévoyons notamment de :

*   **Intégrer les données RF (Radio Frequency) et d'Imagerie Satellite :** L'accès à ces données ouvrira la voie à des analyses multi-sources pour détecter des navires non-AIS, vérifier la conformité des déclarations AIS,
    et identifier des activités potentiellement illicites ou suspectes (ex: pêche illégale, transferts STS furtifs).
*   **Visualisation Complète des Câbles Sous-Marins :** Réintégrer et améliorer l'affichage des câbles sous-marins sur la carte. Cette visualisation est cruciale pour identifier d'éventuelles activités suspectes,
    notamment l'arrachage ou la dégradation de câbles de communication, un risque de plus en plus fréquent.
*   **Développer un Système d'Alertes :** Mettre en place un mécanisme pour générer des alertes automatiques lorsque des activités ou comportements suspects sont détectés par les outils d'analyse.
*   **Créer un Système Historique et de "Rewind" :** Développer la capacité de revenir en arrière dans le temps sur les données AIS et les analyses. Cela permettra d'étudier comment un événement suspect s'est déroulé,
    d'en comprendre le processus pour s'en prémunir à l'avenir, et servira également d'outil de formation puissant.
*   **Développer de Nouveaux Agents et Outils Spécialisés :** Créer des agents experts capables d'analyser des schémas comportementaux plus complexes sur le long terme,
    de corréler les données AIS avec d'autres sources (RF, imagerie, météo avancée, données portuaires), et de répondre à une multitude de questions supplémentaires nécessitant une analyse approfondie et contextuelle.
*   **Renforcer l'Interface Utilisateur :** Ajouter des visualisations avancées (traces historiques, zones d'analyse), améliorer l'interactivité carte-chat, et potentiellement développer une interface web plus robuste.
*   **Optimiser la Gestion des Données :** Mettre en place une base de données plus persistante et performante pour gérer des volumes de données historiques plus importants.

## Contribuer

Ce projet est en cours de développement. Les contributions sont les bienvenues pour améliorer les fonctionnalités, ajouter de nouvelles analyses, affiner l'interface ou corriger des bugs.

