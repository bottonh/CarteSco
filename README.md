# CarteSco
Reconstruction de les contours de la carte scolaire des collèges à l'échelle nationale pour estimer le taux de pauvreté des secteurs.

# Reconstruire les secteurs de la carte scolaire des collèges
A l’été 2021, la Depp a diffusé une [base de données](https://www.data.gouv.fr/en/datasets/carte-scolaire-des-colleges-publics/) associant à chaque adresse postale un collège public. Les informations sont disponibles pour 79 départements. Ces données sont diffusées sous la forme d’un tableau renseignant pour chaque adresse le collège public de secteur:

![image](https://user-images.githubusercontent.com/48832201/191748393-3be47b10-a944-4928-9d49-7dc1fd8c6f05.png)

La première étape consiste donc à transformer cette table en un fichier géographique en géocodant les adresses. Pour cela, les adresses du fichier de la carte scolaire ont été associées à des bases de données associant des coordonnées géographiques à une adresse ([Base Adresse Nationale](https://adresse.data.gouv.fr/),[DVF entre 2016 et 2019](https://adresse.data.gouv.fr/)). Nous obtenons ainsi **8 505 836 adresses géolocalisés que nous pouvons associer à leur collège public de secteur**. 

Pour reconstruire le secteur scolaire associé à chaque collège, nous sommes parti de l'ensemble des adresses qui sont rattachées à ce collège. Sur la carte suivante, chaque point noir correspond à une adresse associée au collège 

<p align="center">
<img src="https://user-images.githubusercontent.com/48832201/191762746-69443115-f95b-4002-91d0-8d58f9afd916.png" />
</p>


La procédure employée pour reconstituer le secteur de chaque collège est détaillée sur la figure 1. La figure 1a représente pour un collège donné l’ensemble des adresses qui y sont associées par la carte scolaire. Pour chaque collège, un polygone a été construit , englobant les adresses rattachées à cet établissement (Figure 1b). L’échantillon final contient 4161 collèges publics en France métropolitaine auxquels sont associés leur secteur de recrutement défini par la carte scolaire. 
