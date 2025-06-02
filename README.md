# 🔐 THC - TTT Hybrid Crypto

**THC** est une bibliothèque de sécurité applicative orientée message, conçue pour offrir des échanges de données sûrs, vérifiables et contextualisés entre utilisateurs ou entités dans un système distribué.

> Elle permet une gestion dynamique des droits d'accès, des signatures automatiques, du suivi d'état, et du partage contrôlé dans des contextes privés ou collaboratifs.

---

## 🚀 Principales Fonctionnalités

- **Messages privés et publics** : échange de contenu texte ou structuré entre utilisateurs.
- **Signatures implicites** : tout contenu est signé via l’identité numérique de son auteur.
- **Lecture contrôlée** : seules les entités autorisées peuvent accéder au contenu.
- **Transfert autorisé ou restreint** : possibilité de partager un contenu avec un tiers selon les règles définies.
- **Données publiques signées** : publication d’informations vérifiables (ex: profil, statistiques).
- **Groupe sécurisé** : discussion ou distribution dans un groupe fermé avec permissions.
- **Suivi d’état** : suivi automatique de l’envoi, réception, lecture (via `TState`).
- **Rotation de clé utilisateur** : changement sécurisé de token et revalidation automatique.

---

## 📚 Exemple de Cas Réels (via `TestTHC`)

```java
// Envoi d’un message privé de u1 à u2
message.content.from(u1).to(u2).set("Bonjour U2");

// Lecture par u2
String text = message.content.from(u1).to(u2).get();

// Rotation sécurisée du token de u1
TNodeToken newToken = new TNodeToken();
newToken.generate(u1.token.get());
u1.token.set(newToken);

// Donnée publique : âge signé
u1.age.securityLevel(THC.SECURE_LOW);
u1.age.from(u1).set(24);

// Groupe sécurisé
group.members.add(u1, u2, u3);
groupMessage.content.from(u3).to(group).set("Hello group!");
```

---

## 🔐 Niveaux de Sécurité

THC fournit trois niveaux de confidentialité définis dans THC :

| Niveau          | Description                                                                    |
| --------------- | ------------------------------------------------------------------------------ |
| `SECURE_LOW`    | Données publiques vérifiables (ex : âge signé)                                 |
| `SECURE_MEDIUM` | Messages partagés (groupe, multi-utilisateur, etc.)                            |
| `SECURE_HIGH`   | Données confidentielles non transférables (ex : messages médicaux, juridiques) |

---

# **THC** : [Voir la documentation](java/README.md)

---