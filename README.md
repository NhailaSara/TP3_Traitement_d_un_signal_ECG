# TP3_Traitement_d_un_signal_ECG

Réalisé par: Sara NHAILA (Filière: BigData Analytics)

Encadré par: Mr Alae Ammour

<h1>Objectifs:</h1>
-Suppression du bruit autour du signal produit par un électrocardiographe.<br/>
-Recherche de la fréquence cardiaque.


# Introduction
Un électrocardiogramme (ECG) est une représentation graphique de l’activation électrique du coeur à l’aide d’un électrocardiographe. Cette activité est recueillie sur un patient allongé, au repos, par des électrodes posées à la surface de la peau. L’analyse du signal ECG est utile dans le but de diagnostiquer des anomalies cardiaques telles qu’une arythmie, un risque d’infarctus, de maladie cardiaque cardiovasculaire ou encore extracardiaque.

Ci-dessous, voici un schéma représentant une représentation classique d’une courbe d’un ECG. Ce schéma se nomme un « Complexe QRS » mettant en évidence le bon fonctionnement d’un cycle cardiaque.


1)Sous Matlab on utilise la commande load pour chargé le signal ECG

Ce signal a été échantillonné avec une fréquence de 50Hz.

#script :

![script1](https://user-images.githubusercontent.com/121027279/215206095-ac86a38e-44c5-4fb1-baac-1e34133fbf6c.png)
![a1](https://user-images.githubusercontent.com/121027279/215206164-d24d3a0c-9606-41c0-b088-47f18cecdfb4.png)

Pour supprimer les bruits à très basse fréquence dues aux mouvements du corps, on utilisera un filtre idéal passe-haut. 
Pour ce faire, on va calculer tout d’abord la TFD du signal ECG, et on régle les fréquences inférieures à 0,5HZ, puis on effectue une TFDI pour restituer le signal filtré.
1)calcule de la TFD du signal ECG :
![s2](https://user-images.githubusercontent.com/121027279/215207189-72a3a27d-e1d9-4d98-b7b6-fc54d06e9095.png)

![script1](https://user-images.githubusercontent.com/121027279/215207226-db0c9245-4bfa-4328-9853-eeaadd5ef270.png)

2- on régle les fréquences inférieures à 0.5Hz à zéro, puis on effectue une TFDI pour restituer le signal filtré.
      
     pass_haut_ideal = ones(size(ecg));
     fc = 0.5;
     %ceil(A) arrondit les éléments de A aux entiers les plus proches supérieurs ou égaux à A. 
     indexe_fc = ceil((fc*N)/fe); 
     pass_haut_ideal(1:indexe_fc)=0;
     pass_haut_ideal(N-indexe_fc+1:N)=0;
     f=(0:N-1)*(fe/N);
     spectre_ecg_filtree = pass_haut_ideal .* spectre_ecg;
     tmp_ecg_filre = ifft(spectre_ecg_filtree,'symmetric');
     plot(t,tmp_ecg_filre);

![a3](https://user-images.githubusercontent.com/121027279/215208957-9f12e6bf-dd8e-485a-b928-1cdd5e247d90.png)

# Suppression des interférences des lignes électriques 50Hz

Souvent, l'ECG est contaminé par un bruit du secteur 50 Hz qui doit être supprimé.

Les filtres Notch sont spécialement conçus pour supprimer la longueur d’onde de votre source d’émission ou d’autres sources de lumières.

Ce filtre permet de supprimer la composante secteur EDF tout en préservant le contenu fréquentiel des signaux de mesures (ECG, SPO2, etc..).
On applique un filtre Notch idéal pour supprimer cette composante. Les filtres Notch sont utilisés pour rejeter une seule fréquence d'une bande de fréquence donnée.

# script :

        % Suppressiondu bruit 50hz
        %retourné un tableau de 1S comme le size de ecg
        pass_notch_ideal = ones(size(ecg));
        fc = 50;
        indexe_fc = ceil((fc*N)/fe)+1;
        pass_notch_ideal(indexe_fc)=0;
        pass_notch_ideal(N-indexe_fc+1)=0;
        spectre_ecg2_filtree = pass_notch_ideal .* fft(tmp_ecg_filre);
        tmp_ecg2_filre = ifft(spectre_ecg2_filtree,'symmetric');
        subplot(311)
        plot(t,ecg);
        subplot(312) plot(t,tmp_ecg2_filre);
        subplot(313) plot(t,ecg-tmp_ecg2_filre);
 ![a4](https://user-images.githubusercontent.com/121027279/215211029-4f4215b4-681a-4e87-819e-11bc037ed391.png)
# Amélioration du rapport signal sur bruit :

Le signal ECG est également atteint par des parasites en provenance de l’activité musculaire extracardiaque du patient. La quantité de bruit est proportionnelle à la largeur de bande du signal ECG. Une bande passante élevée donnera plus de bruit dans les signaux, et limiter la bande passante peut enlever des détails importants du signal.

Après l'appliquation des 3 filtres on a le signal suivant :
![a5](https://user-images.githubusercontent.com/121027279/215211620-806a10af-5323-4b80-9085-417c0a4068d6.png)

![a6](https://user-images.githubusercontent.com/121027279/215212020-d391b817-0ee1-4e1d-a7b1-3e9ee25e1cc9.png)

# Identification de la fréquence cardiaque avec la fonction d’autocorrélation

La fréquence cardiaque peut être identifiée à partir de la fonction d'autocorrélation du signal ECG. Cela se fait en cherchant le premier maximum local après le maximum global (à tau = 0) de cette fonction.
Grâce à la commande xcorr on a pu detecter une correclation dans les maximum a tau égale à 2
     % Identification de la fréquence cardiaque avec la fonction d’autocorrélation 
     subplot(4,3,12)
     [c,lags] = xcorr(ecg_filtre_bas_temp,ecg_filtre_bas_temp);
     stem(lags/fe,c)
   
   <img width="1182" alt="last" src="https://user-images.githubusercontent.com/121027279/215212961-fac5026d-365d-4a1c-8dfe-d83acf27aba3.png">



 
