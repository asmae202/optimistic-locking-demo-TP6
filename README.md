# 🔐 Optimistic Locking avec JPA et Hibernate

# 📁 Table de matières

* [📌 Contexte](#-contexte)

* [❓ Problématique](#-problématique)

* [🎯 Objectif](#-objectif)

- [⚙️ Mecanisme de l-Optimistic-Locking](#-mecanisme-de-l-optimistic-locking)

* [🏗 Implémentation avec @Version](#-implémentation-avec-version)

* [⚡ Simulation d’un Conflit de Concurrence](#-simulation-dun-conflit-de-concurrence)

* [🔄 Gestion des Conflits (Retry)](#-gestion-des-conflits-retry)

* [⚖️ Avantages et Inconvénients](#-avantages-et-inconvénients)

* [🛠 Technologies Utilisées](#-technologies-utilisées)



---

# 📌 Contexte

Dans les applications modernes, plusieurs utilisateurs peuvent modifier les mêmes données en même temps.
Cela peut provoquer des **conflits de mise à jour** et entraîner des incohérences dans la base de données.

Le mécanisme **Optimistic Locking** permet de gérer ces situations en vérifiant que les données n'ont pas été modifiées par un autre utilisateur avant de valider une mise à jour.

---

# ❓ Problématique

Dans un système de réservation :

* Plusieurs utilisateurs peuvent accéder à la même réservation.
* Deux utilisateurs peuvent modifier la réservation simultanément.
* Sans contrôle de concurrence, une modification peut écraser l'autre.

Il est donc nécessaire de **détecter les conflits de modification**.

---

# 🎯 Objectif

Ce TP a pour objectif de :

* Comprendre le **verrouillage optimiste (Optimistic Locking)**
* Implémenter ce mécanisme avec **JPA et Hibernate**
* Simuler un **conflit de modification concurrente**
* Gérer l'exception **OptimisticLockException**
* Implémenter une **stratégie de retry**

---

# ⚙️ Mecanisme de l-Optimistic-Locking

Le **verrouillage optimiste** est une stratégie de gestion de concurrence basée sur l'idée que les conflits sont rares.

Principe :

1. Les utilisateurs peuvent lire et modifier les données librement.
2. Lors de la validation de la modification, la version de l'entité est vérifiée.
3. Si la version a changé entre-temps, la modification est refusée.

Contrairement au **pessimistic locking**, il **n'y a pas de verrouillage au niveau de la base de données**.

---

# 🏗 Implémentation avec @Version

Dans JPA/Hibernate, le verrouillage optimiste est implémenté avec l'annotation suivante :

```
@Version
private Long version;
```

Fonctionnement :

* La version est initialisée lors de la création de l'entité.
* À chaque mise à jour, Hibernate incrémente automatiquement la version.
* Lors d'une modification, Hibernate vérifie que la version correspond à celle en base.
* Si les versions sont différentes, une **OptimisticLockException** est levée.

---

# ⚡ Simulation d’un Conflit de Concurrence

Dans ce projet :

* Deux **threads** récupèrent la même réservation.
* Chaque thread tente de modifier les données.
* Le premier thread valide la modification.
* Le second thread tente de modifier une version obsolète.

Résultat : **un conflit de verrouillage optimiste est détecté**.

---

# 🔄 Gestion des Conflits (Retry)

Deux scénarios sont testés :

### Sans retry

Lorsqu'un conflit est détecté :

* Une **OptimisticLockException** est levée
* La modification échoue

### Avec retry

La stratégie consiste à :

1. Recharger l'entité depuis la base
2. Réappliquer la modification
3. Réessayer la transaction

Cela permet de **résoudre automatiquement certains conflits**.

---

# ⚖️ Avantages et Inconvénients

## Avantages

* Pas de verrouillage au niveau de la base de données
* Bonne performance
* Évite les deadlocks
* Adapté aux applications web

## Inconvénients

* Gestion des exceptions nécessaire
* Implémentation d'une stratégie de retry parfois obligatoire
* Moins efficace si les conflits sont fréquents

---

# 🛠 Technologies Utilisées

* Java
* JPA
* Hibernate
* H2 Database
* Maven

---



