# Chapitre 2 -- La structure imbriquee de l association et sa signature EIP-712

Le standard definit une structure a deux niveaux, reprise fidelement dans
`src/lib/types.ts`. Le niveau interne, `AssociatedAccountRecord` (AAR),
contient la charge utile immuable que les deux parties doivent approuver :
`initiator` et `approver` (les adresses des deux comptes, encodees au format
binaire ERC-7930, voir chapitre 3), `validAt` et `validUntil` (fenetre de
validite temporelle), `interfaceId` (un selecteur optionnel de 4 octets pour
interpreter le champ suivant) et `data` (donnees contextuelles arbitraires,
optionnelles). Le niveau externe, `SignedAssociationRecord` (SAR), enveloppe
cet AAR avec les signatures des deux parties, leur type de cle respectif
(voir chapitre 4), et un champ `revokedAt` (zero tant que l association n a
pas ete revoquee).

Cette separation en deux structures n est pas arbitraire : elle permet de
partager, examiner et faire signer l AAR de maniere asynchrone par chaque
partie -- l initiateur peut signer en premier, se deconnecter, puis
l approbateur se connecte a son tour et signe le meme AAR -- sans jamais
modifier l enregistrement sous-jacent. C est exactement le flux que la demo
implemente : `handleSignInitiator` fait signer l AAR au premier compte
connecte, puis `disconnect()` est appele explicitement pour permettre a
l utilisateur de connecter le second compte, qui appelle ensuite
`handleSignApprover` (`src/hooks/useAssociationSigning.ts`).

La signature elle-meme suit le schema EIP-712 defini par le standard : un
domaine fixe (`{name: "AssociatedAccounts", version: "1"}`, sans `chainId`
ni `verifyingContract` -- l association n est pas liee a une chaine
specifique) et un type unique, `AssociatedAccountRecord`, dont les six champs
sont exactement ceux de l AAR (`src/lib/eip712.ts`). C est ce meme typed-data
qui est signe, que la signature parte vers un stockage on-chain ou hors-
chaine.
