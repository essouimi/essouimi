#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// Structure pour représenter une voiture
typedef struct {
    int id;
    char marque[50];
    char modele[50];
    char carburant[20];
    int places;
    char transmission[20];
    float prix_jour;
    char disponibilite[20];
} Voiture;

// Fonction pour ajouter une voiture
void ajouter_voiture(FILE *fichier) {
    Voiture voiture;
    printf("Entrez les informations de la voiture :\n");
    printf("ID: ");
    scanf("%d", &voiture.id);
    printf("Marque: ");
    scanf("%s", voiture.marque);
    printf("Modele: ");
    scanf("%s", voiture.modele);
    printf("Carburant: ");
    scanf("%s", voiture.carburant);
    printf("Nombre de places: ");
    scanf("%d", &voiture.places);
    printf("Transmission: ");
    scanf("%s", voiture.transmission);
    printf("Prix de location par jour: ");
    scanf("%f", &voiture.prix_jour);
    printf("Disponibilite (disponible ou non disponible): ");
    scanf("%s", voiture.disponibilite);
    
    // Écrire les informations de la voiture dans le fichier
    fprintf(fichier, "%d,%s,%s,%s,%d,%s,%.2f,%s\n", voiture.id, voiture.marque, voiture.modele, voiture.carburant, voiture.places, voiture.transmission, voiture.prix_jour, voiture.disponibilite);
    printf("Voiture ajoutee avec succes.\n");
}

// Fonction pour afficher la liste des voitures
void afficher_liste_voitures(FILE *fichier) {
    Voiture voiture;
    printf("ID\tMarque\tModele\tCarburant\tPlaces\tTransmission\tPrix/Jour\tDisponibilite\n");
    while (fscanf(fichier, "%d,%[^,],%[^,],%[^,],%d,%[^,],%f,%[^\n]\n", &voiture.id, voiture.marque, voiture.modele, voiture.carburant, &voiture.places, voiture.transmission, &voiture.prix_jour, voiture.disponibilite) != EOF) {
        printf("%d\t%s\t%s\t%s\t%d\t%s\t%.2f\t%s\n", voiture.id, voiture.marque, voiture.modele, voiture.carburant, voiture.places, voiture.transmission, voiture.prix_jour, voiture.disponibilite);
    }
}

// Fonction pour supprimer une voiture
void supprimer_voiture(FILE *fichier) {
    int id_suppr;
    printf("Entrez l'ID de la voiture a supprimer: ");
    scanf("%d", &id_suppr);

    Voiture voiture;
    int trouve = 0;

    // Créer un fichier temporaire pour stocker les voitures non supprimées
    FILE *temp = fopen("temp.csv", "w");
    if (temp == NULL) {
        printf("Erreur lors de la suppression de la voiture.\n");
        return;
    }

    // Lire chaque ligne du fichier, ne copier que les voitures non supprimées dans le fichier temporaire
    while (fscanf(fichier, "%d,%[^,],%[^,],%[^,],%d,%[^,],%f,%[^\n]\n", &voiture.id, voiture.marque, voiture.modele, voiture.carburant, &voiture.places, voiture.transmission, &voiture.prix_jour, voiture.disponibilite) != EOF) {
        if (voiture.id != id_suppr) {
            fprintf(temp, "%d,%s,%s,%s,%d,%s,%.2f,%s\n", voiture.id, voiture.marque, voiture.modele, voiture.carburant, voiture.places, voiture.transmission, voiture.prix_jour, voiture.disponibilite);
        } else {
            trouve = 1;
        }
    }

    fclose(fichier);
    fclose(temp);

    // Remplacer le fichier d'origine par le fichier temporaire ne contenant pas la voiture supprimée
    remove("voitures.csv");
    rename("temp.csv", "voitures.csv");

    if (!trouve) {
        printf("Aucune voiture trouvee avec cet ID.\n");
    } else {
        printf("Voiture supprimee avec succes.\n");
    }
}

// Fonction pour rechercher des voitures par marque
void rechercher_par_marque(FILE *fichier) {
    char marque_recherche[50];
    printf("Entrez la marque de voiture a rechercher: ");
    scanf("%s", marque_recherche);

    Voiture voiture;
    int trouve = 0;

    printf("ID\tMarque\tModele\tCarburant\tPlaces\tTransmission\tPrix/Jour\tDisponibilite\n");
    while (fscanf(fichier, "%d,%[^,],%[^,],%[^,],%d,%[^,],%f,%[^\n]\n", &voiture.id, voiture.marque, voiture.modele, voiture.carburant, &voiture.places, voiture.transmission, &voiture.prix_jour, voiture.disponibilite) != EOF) {
        if (strcmp(voiture.marque, marque_recherche) == 0) {
            printf("%d\t%s\t%s\t%s\t%d\t%s\t%.2f\t%s\n", voiture.id, voiture.marque, voiture.modele, voiture.carburant, voiture.places, voiture.transmission, voiture.prix_jour, voiture.disponibilite);
            trouve = 1;
        }
    }

    if (!trouve) {
        printf("Aucune voiture de cette marque n'a ete trouvee.\n");
    }
}

// Fonction pour rechercher des voitures par disponibilité
void rechercher_par_disponibilite(FILE *fichier) {
    char disponibilite_recherche[20];
    printf("Entrez la disponibilite des voitures a rechercher (disponible ou non disponible): ");
    scanf("%s", disponibilite_recherche);

    Voiture voiture;
    int trouve = 0;

    printf("ID\tMarque\tModele\tCarburant\tPlaces\tTransmission\tPrix/Jour\tDisponibilite\n");
    while (fscanf(fichier, "%d,%[^,],%[^,],%[^,],%d,%[^,],%f,%[^\n]\n", &voiture.id, voiture.marque, voiture.modele, voiture.carburant, &voiture.places, voiture.transmission, &voiture.prix_jour, voiture.disponibilite) != EOF) {
        if (strcmp(voiture.disponibilite, disponibilite_recherche) == 0) {
            printf("%d\t%s\t%s\t%s\t%d\t%s\t%.2f\t%s\n", voiture.id, voiture.marque, voiture.modele, voiture.carburant, voiture.places, voiture.transmission, voiture.prix_jour, voiture.disponibilite);
            trouve = 1;
        }
    }

    if (!trouve) {
        printf("Aucune voiture avec cette disponibilite n'a ete trouvee.\n");
    }
}

// Fonction pour trier des voitures par prix de location par jour
void trier_par_prix(FILE *fichier) {
    // Lire toutes les voitures dans un tableau
    Voiture voitures[100]; // Supposons qu'il n'y ait pas plus de 100 voitures
    int nombre_voitures = 0;
    while (fscanf(fichier, "%d,%[^,],%[^,],%[^,],%d,%[^,],%f,%[^\n]\n", &voitures[nombre_voitures].id, voitures[nombre_voitures].marque, voitures[nombre_voitures].modele, voitures[nombre_voitures].carburant, &voitures[nombre_voitures].places, voitures[nombre_voitures].transmission, &voitures[nombre_voitures].prix_jour, voitures[nombre_voitures].disponibilite) != EOF) {
        nombre_voitures++;
    }

    // Trier les voitures par prix de location par jour en utilisant le tri à bulles
    for (int i = 0; i < nombre_voitures - 1; i++) {
        for (int j = 0; j < nombre_voitures - i - 1; j++) {
            if (voitures[j].prix_jour > voitures[j + 1].prix_jour) {
                Voiture temp = voitures[j];
                voitures[j] = voitures[j + 1];
                voitures[j + 1] = temp;
            }
        }
    }

    // Afficher les voitures triées
    printf("ID\tMarque\tModele\tCarburant\tPlaces\tTransmission\tPrix/Jour\tDisponibilite\n");
    for (int i = 0; i < nombre_voitures; i++) {
        printf("%d\t%s\t%s\t%s\t%d\t%s\t%.2f\t%s\n", voitures[i].id, voitures[i].marque, voitures[i].modele, voitures[i].carburant, voitures[i].places, voitures[i].transmission, voitures[i].prix_jour, voitures[i].disponibilite);
    }
}

// Fonction pour gérer l'interface utilisateur
void gestion_interface() {
    FILE *fichier = fopen("voitures.csv", "a+");
    if (fichier == NULL) {
        printf("Erreur lors de l'ouverture du fichier.\n");
        return;
    }

    int choix;
    printf("Bienvenue dans l'application de gestion de location de voitures.\n");

    do {
        printf("\nMenu :\n");
        printf("1. Ajouter une voiture\n");
        printf("2. Afficher la liste des voitures\n");
        printf("3. Supprimer une voiture\n");
        printf("4. Rechercher des voitures par marque\n");
        printf("5. Rechercher des voitures par disponibilite\n");
        printf("6. Trier des voitures par prix de location par jour\n");
        printf("7. Quitter\n");
        printf("Choix: ");
        scanf("%d", &choix);

        switch (choix) {
            case 1:
                ajouter_voiture(fichier);
                break;
            case 2:
                afficher_liste_voitures(fichier);
                break;
            case 3:
                supprimer_voiture(fichier);
                break;
            case 4:
                rechercher_par_marque(fichier);
                break;
            case 5:
                rechercher_par_disponibilite(fichier);
                break;
            case 6:
                trier_par_prix(fichier);
                break;
            case 7:
                printf("Merci d'avoir utilise notre application.\n");
                break;
            default:
                printf("Choix invalide. Veuillez reessayer.\n");
        }
    } while (choix != 7);

    fclose(fichier);
}

// Fonction principale
int main() {
    gestion_interface();
    return 0;
}

