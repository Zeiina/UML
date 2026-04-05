## Diagramme des Cas d'Utilisation

```mermaid
flowchart TD
    subgraph Systeme
        UC1
        UC2
        UC3
        UC4
        UC5
        UC6
        UC7
        UC8
        UC9
    end

    Client((Client))
    Receptionniste((Réceptionniste))
    Administrateur((Administrateur))

    Client --> UC3
    Client --> UC4

    Receptionniste --> UC2
    Receptionniste --> UC3
    Receptionniste --> UC5
    Receptionniste --> UC6
    Receptionniste --> UC7
    Receptionniste --> UC8
    Receptionniste --> UC9

    Administrateur --> UC1

    

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

