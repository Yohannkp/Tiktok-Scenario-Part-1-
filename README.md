Projet TikTok
Cours 2 - Découverte de Python
Bienvenue dans le projet TikTok !

Je viens de commencer en tant que professionnel des données chez TikTok.

L'équipe en est encore aux premières étapes du projet. J'ai reçu une notification indiquant que l'équipe dirigeante de TikTok a approuvé la proposition de projet. Pour obtenir des insights clairs et préparer un modèle de classification des revendications, je dois examiner les données fournies par TikTok afin de lancer le processus d'analyse exploratoire des données (EDA).

Identifier les types de données et compiler les informations résumées
Importation des bibliothèques
python
Copier
Modifier
import pandas as pd
import numpy as np
Ensuite, je charge le dataset dans un DataFrame. Cela va permettre d'effectuer des manipulations de données, une analyse exploratoire des données (EDA) et des activités statistiques.

Remarque : Le dataset a déjà été chargé automatiquement. Je n'ai donc pas besoin de télécharger un fichier .csv ou d'ajouter du code supplémentaire pour y accéder. Je peux directement continuer avec cette analyse.

python
Copier
Modifier
data = pd.read_csv("tiktok_dataset.csv")
Affichage et examen des premières lignes du DataFrame
python
Copier
Modifier
data.head(10)
Obtenir un résumé des informations du DataFrame
python
Copier
Modifier
data.info()
Obtenir des statistiques descriptives
python
Copier
Modifier
data.describe()
Analyse des premières observations
Le DataFrame contient une collection de données catégoriques, textuelles et numériques. Chaque ligne représente une vidéo TikTok distincte, qui peut être une revendication (claim) ou une opinion, accompagnée des métadonnées associées.
Le DataFrame contient cinq colonnes en float64, trois en int64 et quatre en object. Il y a 19 382 observations, mais certaines variables présentent des valeurs manquantes, notamment claim_status, video_transcription_text et plusieurs variables de comptage.
Plusieurs variables de comptage semblent présenter des valeurs aberrantes, notamment aux extrémités supérieures de la distribution. Les écarts-types sont élevés et les valeurs maximales sont très élevées comparées aux quartiles.
Analyse du claim_status
Quels sont les différents statuts de revendication et combien y en a-t-il dans les données ?
python
Copier
Modifier
data['claim_status'].value_counts()
➡️ Analyse : Les proportions des différentes catégories de claim_status sont plutôt équilibrées.

Analyse des tendances d'engagement par statut de revendication
Je vais maintenant examiner l'engagement moyen des vidéos en fonction de leur claim_status.

Nombre moyen de vues pour les vidéos avec le statut "claim"
python
Copier
Modifier
claims = data[data['claim_status'] == 'claim']
print('Nombre moyen de vues pour les revendications :', claims['video_view_count'].mean())
print('Nombre médian de vues pour les revendications :', claims['video_view_count'].median())
Nombre moyen de vues pour les vidéos avec le statut "opinion"
python
Copier
Modifier
opinions = data[data['claim_status'] == 'opinion']
print('Nombre moyen de vues pour les opinions :', opinions['video_view_count'].mean())
print('Nombre médian de vues pour les opinions :', opinions['video_view_count'].median())
➡️ Analyse : La moyenne et la médiane sont proches l'une de l'autre au sein de chaque catégorie de revendication. Cependant, il existe une grande différence dans les nombres de vues entre les vidéos marquées comme revendications et celles marquées comme opinions.

Analyse du statut de bannissement de l'auteur (author_ban_status)
Nombre de vidéos par combinaison de claim_status et author_ban_status
python
Copier
Modifier
data.groupby(['claim_status', 'author_ban_status']).count()[['#']]
➡️ Analyse : Il y a beaucoup plus de vidéos avec des revendications publiées par des auteurs bannis que d'opinions publiées par des auteurs bannis. Cela pourrait signifier plusieurs choses :

Les vidéos de revendications sont plus strictement modérées que les vidéos d’opinions.
Les auteurs de vidéos revendiquant un fait doivent respecter des règles plus strictes que ceux qui publient des opinions.
Analyse des tendances d'engagement liées au author_ban_status
Médiane des partages vidéo selon author_ban_status
python
Copier
Modifier
data.groupby(['author_ban_status']).median(numeric_only=True)[['video_share_count']]
➡️ Analyse : Les vidéos d’auteurs bannis ont un taux de partage médian 33 fois plus élevé que les vidéos d’auteurs actifs !

Statistiques d'engagement selon author_ban_status
python
Copier
Modifier
data.groupby(['author_ban_status']).agg({
    'video_view_count': ['count', 'mean', 'median'],
    'video_like_count': ['count', 'mean', 'median'],
    'video_share_count': ['count', 'mean', 'median']
})
➡️ Observations clés :

Les vidéos d’auteurs bannis ou en cours de révision obtiennent beaucoup plus de vues, de likes et de partages que celles des auteurs actifs.
Pour la plupart des groupes, la moyenne est beaucoup plus grande que la médiane, ce qui suggère la présence de quelques vidéos avec un engagement extrêmement élevé.
Création de nouvelles variables pour mieux comprendre l'engagement
python
Copier
Modifier
data['likes_per_view'] = data['video_like_count'] / data['video_view_count']
data['comments_per_view'] = data['video_comment_count'] / data['video_view_count']
data['shares_per_view'] = data['video_share_count'] / data['video_view_count']
Analyse de l'engagement par claim_status et author_ban_status
python
Copier
Modifier
data.groupby(['claim_status', 'author_ban_status']).agg({
    'likes_per_view': ['count', 'mean', 'median'],
    'comments_per_view': ['count', 'mean', 'median'],
    'shares_per_view': ['count', 'mean', 'median']
})
➡️ Conclusions :

Lorsque l'on prend en compte les vues, le taux d'engagement est davantage lié au claim_status qu'au author_ban_status.
Les vidéos revendicatives ont un taux de likes et d’engagement plus élevé en moyenne que les vidéos d’opinion.
Les auteurs bannis ont des taux de partages et de likes légèrement plus élevés pour les vidéos de revendications.
Résumé final pour l'équipe TikTok
Sur les 19 382 vidéos du dataset, près de 50% sont des revendications (9 608 vidéos).
Le niveau d'engagement est fortement corrélé au claim_status : les vidéos revendicatives ont plus de vues, de likes et de partages que les vidéos d'opinion.
Les vidéos des auteurs bannis reçoivent beaucoup plus d'engagement que celles des auteurs actifs.
Les vidéos contenant des revendications suscitent plus de likes, commentaires et partages en moyenne que les opinions.
➡️ Prochaine étape : Construire un modèle de classification pour prédire le claim_status des vidéos en fonction de leurs métriques d'engagement et des métadonnées associées.

