# Chapitre 5 -- Deux backends de stockage interchangeables, et la revocation en pratique

Conformement a la section "Storage Agnosticism" du standard (qui reconnait
explicitement que differents types d associations ont des besoins
d accessibilite et de cout differents), la demo implemente les deux
approches de stockage evoquees par ERC-8092, activables via un simple choix
d interface (`storageMethod`, `'onchain' | 'database'`, dans
`ControlPanel.tsx`).

Le stockage on-chain (`useAssociationStorage.ts`) appelle
`storeAssociation` sur un contrat `AssociationsStore` deploye sur Base
Sepolia, en reconstruisant la structure Solidity exacte du SAR (avec les
types de cle convertis en `bytes2` via `toHex(keyType, {size: 2})`). Le
contrat emet un evenement `AssociationCreated` dont le premier topic indexe
sert d identifiant unique de l association ; la demo le recupere en
decodant les logs de la transaction confirmee (`decodeEventLog`). La
revocation on-chain (`useAssociationRevocation.ts`) appelle simplement
`revokeAssociation` avec cet identifiant et un timestamp, le contrat faisant
foi pour toute logique de validation (autorisation, horodatage).

Le stockage hors-chaine passe par une route API Next.js
(`src/app/api/associations/route.ts`) adossee a une base Postgres Neon
(`src/lib/db.ts`). Avant toute ecriture, le serveur revalide integralement
l association via `validateAssociation` -- exactement la meme fonction de
validation utilisee cote lecture -- ce qui garantit qu aucune association
invalide (signature incorrecte, fenetre de validite expiree) ne peut etre
persistee, meme si le frontend a un bug. La revocation hors-chaine (route
PATCH) illustre un detail de securite interessant : puisqu il n y a pas de
contrat pour faire respecter les regles d autorisation, le serveur exige que
le compte demandant la revocation signe un message texte au format exact
`Revoke association {id} at timestamp {timestamp}` (verifie via
`verifyMessage`), et verifie ensuite explicitement que l adresse signataire
est bien l initiateur ou l approbateur enregistre pour cette association --
reproduisant hors-chaine la meme regle d autorisation qu un contrat aurait
imposee nativement. Dans les deux backends, la regle du standard "en cas de
revocation multiple, l horodatage le plus ancien fait foi" est respectee
explicitement (comparaison `revoked_at IS NULL OR revoked_at > timestamp`
cote SQL, ou logique equivalente cote contrat).
