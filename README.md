# CarteSco
Reconstruction des contours de la carte scolaire des collèges à l'échelle nationale pour estimer le taux de pauvreté des secteurs.

# Reconstruire les secteurs de la carte scolaire des collèges
À l’été 2021, la Depp a diffusé la [sectorisation des collèges](https://www.data.gouv.fr/en/datasets/carte-scolaire-des-colleges-publics/) à l'échelle nationale, associant à chaque adresse postale un collège public. Les informations sont disponibles pour 79 départements. Ces données sont diffusées sous la forme d’un tableau renseignant pour chaque adresse le collège public de secteur:

![image](https://user-images.githubusercontent.com/48832201/191748393-3be47b10-a944-4928-9d49-7dc1fd8c6f05.png)

La première étape consiste donc à transformer cette table en un fichier géographique en géolocalisant les adresses. Pour cela, les adresses du fichier de la carte scolaire ont été associées à des bases de données associant des coordonnées géographiques à une adresse ([Base Adresse Nationale](https://adresse.data.gouv.fr/),[DVF entre 2016 et 2019](https://www.data.gouv.fr/fr/datasets/demandes-de-valeurs-foncieres/)). 

Nous obtenons ainsi **8 505 836 adresses géolocalisés que nous pouvons associer à leur collège public de secteur**. 

Pour reconstruire le secteur scolaire associé à chaque collège: 

1. Nous sommes partis de l'ensemble des adresses qui sont rattachées à ce collège. Sur la carte suivante, chaque point noir correspond à une adresse associée au collège représenté par <img src="https://user-images.githubusercontent.com/48832201/191766079-0f5b4faf-7726-4689-8edb-1e6de94f1dca.png" width="20">

<p align="center">
<img src="https://user-images.githubusercontent.com/48832201/191766866-4097e7f0-ffa4-4a10-9a11-af3d2bc1a92e.png"  width="400">
</p>

2. Un polygone a été construit, englobant les adresses rattachées à cet établissement[^1]. La fonction *ashape* du package *alphahull* sur R a été utilisée. L’algorithme permettant d’estimer ces polygones est basé sur la triangulation de Delaunay. 

<p align="center">
<img src="https://user-images.githubusercontent.com/48832201/191770183-130ca881-7147-454c-b977-3ddc48960d32.png"  width="400">
</p>

L’échantillon final contient 4161 collèges publics en France métropolitaine auxquels est associé leur secteur de recrutement défini par la carte scolaire. 

# Estimer le taux de pauvreté des secteurs scolaires reconstruits
Après avoir reconstruit les limites des secteurs scolaires, nous estimons leur taux de pauvreté à partir des [données carroyées](https://www.insee.fr/fr/statistiques/6215138?sommaire=6215217#consulter) diffusées par l'Insee. Ces données concernent les revenus perçus en 2017 et sont diffusées à l'échelle de carreaux de 200 mètres de côté. 

Nous agrégeons pour chaque secteur scolaire le nombre de ménages total et le nombre de ménages dont le niveau de vie est inférieur au seuil de pauvreté. Le nombre de ménages comptabilisés est proportionnel à la surface du carreau intersectée par le secteur[^2]. 

Sur la carte suivante, les carreaux les plus rouges ont un taux de pauvreté plus élevé.

<p align="center">
<img src="https://user-images.githubusercontent.com/48832201/191770250-50ebaee8-346a-4380-921d-8c60ea442111.png"  width="400">
</p>

## Correction de l'estimation du taux de pauvreté
Pour tester la qualité de la méthode d’estimation du taux de pauvreté des secteurs scolaires, le taux de pauvreté des Iris a été estimé et comparé au taux de pauvreté [directement diffusé par l’Insee](https://www.insee.fr/fr/statistiques/4217503#consulter) à cette échelle géographique. La corrélation entre le taux de pauvreté estimé et le taux de pauvreté observé des Iris est représentée sur le graphique suivant:

<p align="center">
<img src="https://user-images.githubusercontent.com/48832201/191904953-5f311801-933e-4209-8ce8-caa40dd4c0ce.png"  width="400">
</p>

Le R<sup>2</sup> est de 92,7% mais le coefficient n'est que de 0,754. Il apparaît, en effet, que les taux de pauvreté les plus élevés sont systématiquement sous-estimés[^3]. Pour améliorer la qualité de l’estimation, l’écart médian entre le taux de pauvreté estimé et le taux de pauvreté observé été calculé pour chaque vingtile d’Iris et ajouté à l’estimation initiale. La corrélation entre le taux de pauvreté estimé (et corrigé) et le taux de pauvreté observé est donc de bien meilleure qualité :

<p align="center">
<img src="https://user-images.githubusercontent.com/48832201/191905110-6efde775-e80c-4c25-a9f1-d6b85c3b1e28.png"  width="400">
</p>

Le R<sup>2</sup> est de 95,7% et le coefficient est de 0,987. Les écarts entre le taux de pauvreté estimé et observé sont appliqués de la même manière aux taux de pauvreté des secteurs scolaires pour améliorer la précision de l'estimation.  

## Comparaison du taux de pauvreté estimé à partir des secteurs reconstruits et des secteurs officiels
Une fois la méthode d'estimation du taux de pauvreté corrigée, nous vérifions la qualité de la reconstruction des contours des secteurs scolaires. La [Loire-Atlantique](https://data.paysdelaloire.fr/explore/dataset/224400028_carte-scolaire-des-colleges-publics-de-loire-atlantique%40loireatlantique/export/?disjunctive.commune&location=11,47.29693,-1.45638&basemap=jawg.streets) et la [Métropole de Lyon](https://data.grandlyon.com/jeux-de-donnees/secteurs-colleges-metropole-lyon/donnees) diffusent directement en libre accès les contours géographiques des secteurs scolaires des collèges implantés sur leur territoire. En appliquant la méthodologie précédemment décrite, le taux de pauvreté des secteurs officiels a pu être estimé et comparé à celui des secteurs dont les contours ont été reconstruits. La relation entre ces deux taux de pauvreté est parfaitement linéaire (coefficient = 0.997) et comporte très peu d’erreurs (R<sup>2</sup> = 99%), ce qui assure la robustesse des analyses développées dans la suite de l’analyse. 

<p align="center">
<img src="https://user-images.githubusercontent.com/48832201/191904446-c381af42-f5d7-4a68-abe8-64816f458eb2.png"  width="400">
</p>

[^1]: Pour limiter l’influence de points aberrants sur la forme des polygones, les points trop isolés (dont la longitude ou la latitude est hors de la médiane du groupe +/- 3 écart-types) ont été supprimés. Pour s’assurer de la robustesse des résultats, un secteur a été retiré car sa superficie était trop petite (moins de 0.1 km<sup>2</sup>), résultant sûrement d’une mauvaise saisie des données ou d'un problème de géolocalisation. De même, deux secteurs ont aussi été retirés de l’analyse car ils regroupaient trop d’individus. Ces secteurs ont été détectés en regardant les polygones suffisamment grands (superficie supérieure à 10 km<sup>2</sup>) et regroupant plus de 30000 ménages.

[^2]: Si un carreau, en bordure d'un secteur scolaire, n'est intersecté que sur la moitié de sa surface alors la moitié des ménages habitants dans ce carreau seront assignés au secteur scolaire. Cette méthodologie repose sur l'hypothèse d'une répartition homogène des ménages au sein du carreau, ce qui n'est pas aberrant compte tenu de la faible superficie des carreaux (200m de côté). 

[^3]: Ce biais peut notamment s’expliquer par le secret statistique appliqué aux données carroyées. Les carreaux avec les taux de pauvreté les plus élevés sont majorés à 80% pour respecter le secret statistique, conduisant mécaniquement à une sous-estimation des taux de pauvreté des zones dans lesquels sont situés ces carreaux. Ainsi, 46 carreaux ont un taux de pauvreté majoré à 80%.
