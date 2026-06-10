# -LAB-15-Analyse-Dynamique-Android-Inspection-TLS-HTTPS-et-Gestion-du-SSL-Pinning
# 🌐 LAB 15 : Analyse Dynamique Android — Inspection TLS/HTTPS et Contournement du SSL Pinning

## 📝 Description du Projet
Ce laboratoire est dédié à l'**interception et l'analyse des flux réseaux sécurisés** (HTTP/HTTPS) d'une application Android[cite: 9]. L'objectif est de mettre en place un proxy d'interception (comme Burp Suite) pour analyser l'exposition des données en transit, puis d'utiliser l'instrumentation dynamique pour réaliser un **Bypass de SSL Pinning**, neutralisant ainsi la validation des certificats côté client[cite: 9].

---

## 🛠️ Outils & Environnement de Test
* **Proxy d'interception :** Burp Suite / OWASP ZAP (Analyse des requêtes/réponses)
* **Instrumentation dynamique :** Frida Toolkit (Scripts d'interception SSL)
* **Environnement cible :** Émulateur Android connecté via ADB avec certificat proxy installé

---

## 🚀 Étapes de l'Audit & Preuves en Images

### Étape 1 : Configuration du Proxy et Forwarding Réseau
Mise en place de la configuration réseau sur l'appareil Android pour rediriger le trafic vers le proxy d'interception de la machine hôte.
<img width="724" height="42" alt="Configuration du proxy réseau via ADB" src="https://github.com/user-attachments/assets/268f8923-ac66-4e86-be4f-3f829c333534" />

### Étape 2 : Initialisation du serveur Frida sur l'appareil
Démarrage et vérification du processus `frida-server` sur l'émulateur pour permettre l'injection des scripts de bypass TLS.
<img width="1023" height="236" alt="Vérification du statut de Frida-Server" src="https://github.com/user-attachments/assets/381b299e-f6e8-4748-9bc7-4f6361faf09b" />

### Étape 3 : Tentative d'interception et blocage du trafic (SSL Pinning Actif)
L'application refuse de communiquer et l'interception échoue car l'APK valide la signature du certificat et détecte la présence du proxy (Erreur de poignée de main TLS / Handshake).
<img width="780" height="520" alt="Blocage du trafic par le mécanisme de SSL Pinning" src="https://github.com/user-attachments/assets/d7563133-51a1-4e1c-9f47-e351a8698492" />

### Étape 4 : Injection du script de SSL Pinning Bypass
Lancement de Frida avec un script universel pour hooker les classes de confiance (`TrustManager`, `OkHttpClient`, `WebView`) et forcer l'application à accepter le certificat du proxy.
<img width="1385" height="93" alt="Injection du script Frida pour le bypass SSL" src="https://github.com/user-attachments/assets/728e2c36-3eea-42e4-ba8d-c5e358deea04" />

### Étape 5 : Interception réussie des requêtes HTTPS en clair
Une fois le bypass activé, l'application fonctionne normalement et l'ensemble du trafic chiffré (paramètres, clés API, requêtes d'authentification) devient visible en clair sur le proxy.
<img width="1409" height="242" alt="Interception réussie des flux HTTPS sur le Proxy" src="https://github.com/user-attachments/assets/cf391d26-bb1f-4b00-a5e9-0ceaa8789288" />

---

## 📊 Informations du Rapport

* **Auditeur :** Anas El Mahfoudy
* **Établissement :** École Marocaine des Sciences de l'Ingénieur (EMSI)
* **Domaine :** Sécurité Mobile / Audit des Flux Réseaux[cite: 9]

---

## 🔍 Concepts Avancés Traités
1. **SSL Pinning (Épinglage de certificat) :** Mécanisme de sécurité qui consiste à hardcoder le certificat du serveur dans l'application mobile pour empêcher les attaques de type Man-In-The-Middle (MITM).
2. **Runtime Hooking vs Réseau :** L'analyse démontre que même si le chiffrement TLS est robuste, le fait de modifier dynamiquement la logique de validation de l'application en mémoire permet de contourner entièrement cette sécurité.
