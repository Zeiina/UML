## Diagramme des Cas d'Utilisation

```mermaid
graph TD
    subgraph Logiciel_Gestion_Hoteliere
        title[Logiciel de Gestion Hôtelière]
        
        %% Cas d'utilisation
        uc1[Maintenir Parc Hôtelier]
        uc2[Consulter Disponibilités]
        uc3[Effectuer Réservation]
        uc4[Enregistrer Arrhes]
        uc5[Enregistrer Arrivée Client]
        uc6[Enregistrer Consommations]
        uc7[Établir Facture]
        uc8[Éditer Liste Arrivées]
        uc9[Éditer État Occupation]
    end

    %% Acteurs
    act_client((Client))
    act_reception((Réceptionniste))
    act_admin((Administrateur))

    %% Associations
    act_client --> uc3
    act_client --> uc4
    act_reception --> uc2
    act_reception --> uc3
    act_reception --> uc5
    act_reception --> uc6
    act_reception --> uc7
    act_reception --> uc8
    act_reception --> uc9
    act_admin --> uc1

    

## Diagramme de Classes

```mermaid
classDiagram
    class Hotel {
        -int id
        -string nom
        -int nbEtoiles
        -string adresse
        +modifierCaracteristiques()
        +consulterDisponibilites()
    }

    class Chambre {
        -int numero
        -int capacite
        -float prixBase
        -boolean estLibre
        +calculerPrix(int personnes)
        +reserver()
        +liberer()
    }

    class CategorieChambre {
        -int id
        -string description
        -string degreConfort
        -float prixParNuit
    }

    class Client {
        -int codeClient
        -string nom
        -string email
        -string telephone
        +fournirInfos()
    }

    class Reservation {
        -int numeroResa
        -date dateReservation
        -date dateDebut
        -date dateFin
        -float montantTotal
        +calculerMontant()
    }

    class Facture {
        -int numeroFacture
        -float totalPrestations
        -float totalSejour
        -float montantTotal
        +editerFacture()
    }

    class Prestation {
        -int id
        -string libelle
        -date date
        -float prix
    }

    %% Relations (Cardinalités)
    Hotel "1" -- "1..*" CategorieChambre : possède
    CategorieChambre "1" -- "1..*" Chambre : classifie
    Chambre "1" -- "0..1" Reservation : concerne
    Client "1" -- "0..*" Reservation : effectue
    Reservation "1" -- "0..1" Facture : genere
    Reservation "1" -- "1..*" Prestation : contient
    Hotel "1" -- "*" Prestation : propose

    


## Diagramme d'Activité du Processus de Réservation

```mermaid
flowchart TD
    Start([Début]) --> ChooseMethod{Choix du canal}
    
    %% Swith entre les deux méthodes
    ChooseMethod -->|Internet| FillForm[Client remplit formulaire web]
    ChooseMethod -->|Agence| FillPaper[Client remplit imprimé papier]
    
    %% Fusion des deux flux après saisie
    FillForm --> EnterData[Saisie des infos: <br/>Nom, Dates, Classe, Catégorie]
    FillPaper --> EnterData
    
    %% Vérification dispo
    EnterData --> CheckDispo{Demande soluble ?}
    CheckDispo -->|Non| NotifyFail[Notifier client indisponibilité]
    NotifyFail --> End([Fin])
    
    %% Si dispo
    CheckDispo -->|Oui| CreateResa[Créer réservation <br/>avec numéro unique]
    CreateResa --> CheckDelay{Délai > 8 jours ?}
    
    %% Gestion des arrhes
    CheckDelay -->|Non| ConfirmDirect[Réservation confirmée immédiate]
    CheckDelay -->|Oui| RequestArhes[Demander arrhes >= 10%]
    RequestArhes --> PayArhes[Client paie les arrhes]
    PayArhes --> ConfirmDirect
    
    %% Fin
    ConfirmDirect --> Save[Enregistrer réservation]
    Save --> End

    
## Diagramme de Séquence 

```mermaid
sequenceDiagram
    participant Client
    participant InterfaceWeb as Interface Web
    participant Systeme as Système de Gestion
    participant DB as Base de Données

    Client->>InterfaceWeb: Remplir formulaire (dates, catégorie)
    InterfaceWeb->>Systeme: Soumettre demande disponibilité
    Systeme->>DB: Vérifier chambres libres
    DB-->>Systeme: Retourne liste chambres dispo
    Systeme-->>InterfaceWeb: Affiche choix disponibles
    InterfaceWeb-->>Client: Propose chambre
    
    Client->>InterfaceWeb: Confirme la réservation
    InterfaceWeb->>Systeme: Envoie confirmation
    
    alt Si délai > 8 jours
        Systeme->>Systeme: Calculer arrhes (10%)
        Systeme-->>Client: Demande paiement arrhes
        Client-->>Systeme: Fournit paiement
    else Si délai <= 8 jours
        Systeme->>Systeme: Réservation immédiate
    end
    
    Systeme->>DB: Enregistrer la réservation
    DB-->>Systeme: Confirmation enregistrement
    Systeme-->>Client: Envoie numéro de réservation

