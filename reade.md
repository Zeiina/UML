# Cartographie des flux – Livraison logicielle

## État actuel (Avant DevOps)

```mermaid
graph TD
A[Développeur termine une fonctionnalité] --> B[Fin de sprint - Livraison bloc]
B --> C[Équipe d'exploitation externe]
C --> D[Déploiement manuel en production]
D --> E[Bugs détectés par utilisateurs]
E --> F[Retour tardif]
F --> G[Correction longue]

style A fill:#ffcccc
style B fill:#ffcccc
style C fill:#ffcccc
style D fill:#ffcccc
style E fill:#ffcccc
style F fill:#ffcccc
style G fill:#ffcccc

graph TD
A[Git push] --> B[Pipeline CI déclenché]
B --> C[Tests unitaires + intégration]
C --> D[Analyse code + sécurité]
D --> E[Déploiement automatique sur staging]
E --> F[Tests fonctionnels automatisés]
F --> G{Validation manuelle rapide?}
G -->|OK| H[Déploiement production]
H --> I[Monitoring + alerting temps réel]
I --> J[Rollback automatique si anomalie]

style A fill:#ccffcc
style B fill:#ccffcc
style C fill:#ccffcc
style D fill:#ccffcc
style E fill:#ccffcc
style F fill:#ccffcc
style G fill:#ccffcc
style H fill:#ccffcc
style I fill:#ccffcc
style J fill:#ccffcc
