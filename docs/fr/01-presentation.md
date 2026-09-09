# Chapitre 1 -- Presentation de ERC-8092-demo

Ce depot est une application Next.js de demonstration officielle de Base pour
ERC-8092 ("Associated Accounts"), une proposition de standard Ethereum encore
au stade brouillon (statut "Draft") au moment de l ecriture, dont le texte
complet est inclus dans le depot lui-meme (`erc-8092.md`). Le probleme que ce
standard adresse est distinct de celui du depot nfc-relayer documente
precedemment dans cette bibliotheque (qui referencait un "ERC-XXXXX" pour le
paiement via NFC) : ERC-8092 ne concerne pas les paiements, mais la capacite
pour deux comptes blockchain de declarer publiquement, prouver et revoquer une
relation entre eux, via une charge utile standardisee et signee par les deux
parties.

Le cas d usage central mis en avant par le standard est l "identite heritee
de sous-compte" (sub-account identity inheritance) : aujourd hui, un
utilisateur qui possede plusieurs adresses pour des contextes differents
(une adresse par application, par exemple) n a aucun moyen standardise de
prouver publiquement que ces adresses appartiennent a la meme identite,
sans sacrifier la separation entre elles. ERC-8092 fournit ce mecanisme de
liaison verifiable, avec des donnees contextuelles arbitraires attachees
(delegation d autorisation, agregation de reputation, etc.).

La demo elle-meme met en scene un flux complet entre deux comptes : un
"initiateur" et un "approbateur" (approver), qui signent chacun de leur cote
un message EIP-712 decrivant l association, avant que celle-ci ne soit
stockee soit on-chain (dans un smart contract deploye sur Base Sepolia), soit
hors-chaine (dans une base de donnees Postgres via une route API Next.js).
Un graphe interactif (via la librairie Cytoscape) visualise ensuite les
associations existantes pour une adresse donnee.
