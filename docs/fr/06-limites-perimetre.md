# Chapitre 6 -- Limites et perimetre de ce parcours

Ce parcours couvre la structure imbriquee AAR/SAR definie par ERC-8092 et sa
justification, le schema de signature EIP-712 utilise pour faire approuver
une association par ses deux parties de maniere asynchrone, le role des
adresses interoperables ERC-7930 pour rester agnostique au type de chaine,
les trois types de comptes signataires geres par la demo (EOA via K1, smart
contract deploye via ERC-1271, smart contract contrefactuel via ERC-6492) et
leur logique de detection, ainsi que les deux backends de stockage
interchangeables (contrat on-chain sur Base Sepolia, base de donnees
Postgres hors-chaine) avec leurs mecanismes de revocation respectifs.

Sont volontairement laisses hors champ : le detail de l implementation
Solidity du contrat `AssociationsStore` lui-meme (la demo n en consomme que
l ABI, pas le code source) ; le composant de visualisation en graphe
(`AssociationsGraph.tsx`, base sur Cytoscape), dont la logique de rendu est
une couche de presentation sans rapport avec la mecanique du standard ; la
gestion fine des types de cles non implementes par la demo bien que prevus
par le standard (BLS, EdDSA, Delegated) ; et le detail de la detection de
portefeuille intelligent (`useSmartWallet.ts`), qui s appuie sur des
heuristiques (identifiant de connecteur, capacites `atomicBatch`/
`paymasterService`) propres a l ecosysteme wagmi plutot que sur le standard
lui-meme.

Un point de clarification important : bien que le depot nfc-relayer,
documente precedemment dans cette bibliotheque, mentionne un "ERC-XXXXX" non
numerote pour la transmission de paiements via NFC, ce standard n a aucun
rapport avec ERC-8092 -- il s agit de deux propositions Ethereum totalement
distinctes, l une portant sur les paiements sans contact, l autre sur les
associations verifiables entre comptes. L objectif de ce parcours est de
comprendre precisement comment cette demo illustre le fonctionnement d
ERC-8092 (signature, validation, stockage, revocation), pas de statuer sur
l etat final ou l adoption eventuelle du standard, qui reste au stade
"Draft" au moment de l ecriture de ce depot.
