# Comparaison technique : AWS FSx vs S3 Storage Gateway  
**Cas d'usage** : *Analyse pour un environnement 100 % cloud avec instances EC2*

---

## **Architecture et déploiement**

| Critère | AWS FSx | AWS S3 Storage Gateway |
|---------|---------|------------------------|
| **Type de service** | Service de fichiers entièrement managé | Passerelle avec cache local et backend S3 |
| **Modèle de déploiement** | Service AWS natif (aucune instance à gérer) | Instance EC2 dédiée(contient le gateway) + volumes EBS + bucket S3 |
| **Protocoles supportés** | Support Natif : SMB, NFS (selon le type FSx) | SMB, NFS, iSCSI (File Gateway) |
| **Intégration cloud** | Connexion directe depuis les instances EC2 (port 445/2049) | Les EC2 se connectent à l’instance Gateway, qui relaye vers S3 |


---

## **Performances et accès aux données**

| Critère | AWS FSx | AWS S3 Storage Gateway |
|---------|---------|------------------------|
| **Latence** | Faible | Moyenne à élevée (cache EBS → S3) – dépend du cache hit et passage *user(ec2) -> ec2 gateway -> s3* |
| **Consistance des données** | Forte (écritures immédiates) | Éventuelle (écritures asynchrones vers S3) |
| **Cache local** | Non nécessaire (performance native) | Obligatoire |
| **Accès SMB natif** | Oui, direct depuis EC2 | Non, mais via l’instance Gateway |

---

## **Capacité et évolutivité**

| Critère | AWS FSx | AWS S3 Storage Gateway |
|---------|---------|------------------------|
| **Capacité minimale** | 32 GB | Aucune (selon S3) |
| **Capacité maximale** | 64 TB per FS | Illimitée (backend S3) |
| **Support pour Scaling** | OUI | OUI |

---


## **Modèle de coûts (estimation pour 10 TB)**

| Poste de coût | AWS FSx (Single-AZ) | S3 Storage Gateway |
|---------------|----------------------|---------------------|
| **Stockage principal** | 10 TB | 10 TB |
| **Instance/Service** | Non | EC2 m5.large : |
| **Cache local** | Non applicable | EBS 2 TB gp3 |
| **Backups** | Inclus | Lifecycle policies S3 (inclus) |
| **Coût mensuel estimé** | **≈ 794 $** | **≈ 522 $** |
| **Lien vers le pricing calculator** | [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=6f132e09997f52a5af52fe142f10149a2e679a3c) | [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=259e3a736e306ed12476ee39715976608e7f5074) |

> *Les coûts peuvent varier selon la région, le débit provisionné (FSx) et les requêtes S3.*

> *Région dans l'exemple : Europe (Francfort)*


## **Recommandations**

| Besoin | Recommandation |
|--------|----------------|
| **Performances optimisées pour notre cas d'étude** |  **FSx** (latence faible, débit garanti) |
| **Coût minimal** |  **Solution avec S3 Storage Gateway** (Le cost dépend fortement de la EC2 Gateway) |
| **Simplicité opérationnelle** |  **FSx** (AWS gère tout) |
| **Évolutivité à long terme** |  **S3 Storage Gateway** (illimité via S3) |

---

