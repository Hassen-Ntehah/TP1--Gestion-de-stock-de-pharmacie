# TP 1 : Gestion de Stock de Pharmacie (Spring Boot - MicroServices)
**Professeur :** Youness IDRISSI KHAMLICHI  
**Établissement :** ENSA Fès  
**Module :** Spring Boot - MicroServices  

---

## 📌 Objectif du TP
Développer une application distribuée de gestion du stock d'une pharmacie comprenant :
- La gestion complète des produits pharmaceutiques
- Base de données MySQL (`pharmacie_db`) & fallback H2
- Relations JPA complètes : `@OneToOne`, `@OneToMany`, `@ManyToOne`, `@ManyToMany`
- Interface Web Thymeleaf pour la consultation et gestion visuelle
- API RESTful complète avec codes HTTP conformes (200, 201, 204, 404)
- Jeu de données complet initialisé automatiquement au démarrage
- Tests unitaires et d'intégration validés
- Collection Postman prête à l'emploi

---

## 🗄️ Modèle de Données & Relations JPA

### 1. Structure du Modèle
- **`Categorie`** (`id`, `nom`, `description`) :
  - `@OneToMany(mappedBy = "categorie")` vers `Produit`
- **`Fournisseur`** (`id`, `nom`, `contact`, `adresse`) :
  - `@OneToMany(mappedBy = "fournisseur")` vers `Produit`
- **`DetailProduit`** (`id`, `fabricant`, `paysOrigine`, `composition`, `dosage`, `indications`) :
  - `@OneToOne(mappedBy = "detailProduit")` vers `Produit`
- **`Tag`** (`id`, `libelle`) :
  - `@ManyToMany(mappedBy = "tags")` vers `Produit`
- **`Produit`** (`id`, `nom`, `description`, `prix`, `code_barre`, `date_expiration`, `categorie_id`, `fournisseur_id`, `detail_id`) :
  - `@ManyToOne` vers `Categorie`
  - `@ManyToOne` vers `Fournisseur`
  - `@OneToOne(cascade = CascadeType.ALL, orphanRemoval = true)` vers `DetailProduit`
  - `@ManyToMany` avec table de jointure `produit_tag (produit_id, tag_id)` vers `Tag`

---

## 🚀 Lancement Rapide

### Prérequis
- Java 17 ou supérieur
- MySQL démarré sur le port 3306 (Base: `pharmacie_db`, User: `root`, Password: `root` ou vide)  
  *(Note : Si MySQL n'est pas démarré, vous pouvez exécuter en mémoire H2 avec `--spring.profiles.active=h2`).*

### Démarrage
```bash
cd /Users/owner/.gemini/antigravity-ide/scratch/pharmacie-stock
./mvnw spring-boot:run
```
*(Si le TP2 `pharma_security` tourne déjà sur le port 8080, lancez sur le port 8081 :)*
```bash
./mvnw spring-boot:run -Dspring-boot.run.arguments="--server.port=8081"
```

### Exécution des Tests
```bash
./mvnw clean test
```
*(11/11 tests passent avec succès : contrôleurs REST, Thymeleaf, relations en cascade).*

---

## 🌐 Interfaces & Endpoints

### 1. Interface Web (Thymeleaf)
- **Liste des produits :** [http://localhost:8080/](http://localhost:8080/) ou [http://localhost:8080/produits](http://localhost:8080/produits)
- **Ajout d'un produit :** [http://localhost:8080/produits/nouveau](http://localhost:8080/produits/nouveau)
- **Fiche détail :** [http://localhost:8080/produits/details/1](http://localhost:8080/produits/details/1)
- **Catégories :** [http://localhost:8080/categories](http://localhost:8080/categories)
- **Fournisseurs :** [http://localhost:8080/fournisseurs](http://localhost:8080/fournisseurs)

### 2. Routes API REST (Conformes au Sujet)

| Méthode | URL | Description | Statut HTTP |
| :--- | :--- | :--- | :--- |
| `GET` | `/api/produits` | Lister tous les produits | `200 OK` |
| `GET` | `/api/produits/{id}` | Consulter un produit avec détails | `200 OK` / `404` |
| `POST` | `/api/produits` | Ajouter un produit (avec catégorie, fournisseur, détails et tags) | `201 CREATED` |
| `PUT` | `/api/produits/{id}` | Mettre à jour un produit existant | `200 OK` / `404` |
| `DELETE` | `/api/produits/{id}` | Supprimer un produit | `204 NO CONTENT` |
| `GET` | `/api/categories` | Lister les catégories | `200 OK` |
| `POST` | `/api/categories` | Ajouter une catégorie | `201 CREATED` |
| `PUT` | `/api/categories/{id}` | Modifier une catégorie | `200 OK` |
| `GET` | `/api/fournisseurs` | Lister les fournisseurs | `200 OK` |
| `POST` | `/api/fournisseurs` | Ajouter un fournisseur | `201 CREATED` |
| `PUT` | `/api/fournisseurs/{id}` (ou `/fournisseurs/{id}`) | Modifier un fournisseur | `200 OK` |
| `GET` | `/api/tags` | Lister les tags/étiquettes | `200 OK` |

---

## 🧪 Collection Postman Fournie
Le fichier Postman officiel est situé à la racine du projet :
📁 `pharmacie_stock_postman_collection.json`

Contient tous les cas de test prédéfinis du TP :
1. Ajout de catégorie (`Anti-inflammatoires`) - Page 5
2. Ajout de fournisseur (`Novartis`) - Page 5
3. Ajout de produit complet (`Voltaren 50mg`) - Page 5
4. Modification de produit (`Doliprane 500mg (Nouveau)`) - Page 6
5. Modification de fournisseur (`Sandoz Pharma`) - Page 6
6. Modification de catégorie (`Antalgiques & Anti-douleurs`) - Page 6
7. Suppression de produit (HTTP 204)
