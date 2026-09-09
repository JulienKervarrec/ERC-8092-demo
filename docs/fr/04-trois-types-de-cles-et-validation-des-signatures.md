# Chapitre 4 -- Trois types de comptes signataires : EOA, contrat deploye, contrat non deploye

ERC-8092 definit une table de types de cles (Key IDs) extensible, ou le bit
de poids fort distingue les courbes cryptographiques classiques (K1/secp256k1,
R1/secp256r1, BLS, EdDSA) des integrations de protocole (WebAuthn, ERC-1271,
ERC-6492). La demo implemente concretement trois de ces types, couvrant les
trois situations les plus courantes pour un portefeuille Ethereum moderne.

Le type `K1` correspond a une adresse a cle privee classique (EOA) : la
signature est verifiee directement via `verifyTypedData` de viem
(`src/lib/validation.ts`), en recuperant l adresse qui a produit la
signature et en la comparant a l adresse attendue. Le type `ERC1271`
correspond a un smart contract wallet deja deploye sur la chaine : la
verification appelle la fonction `isValidSignature` du contrat lui-meme
(`src/lib/erc1271.ts`), qui doit retourner la valeur magique `0x1626ba7e`
pour confirmer la validite. Le type `ERC6492` correspond au cas plus subtil
d un smart contract wallet qui n est pas encore deploye au moment de la
signature (un wallet "contrefactuel", dont l adresse est connue a l avance
mais dont le code n existe pas encore on-chain) : la signature porte alors un
suffixe magique specifique de 32 octets (`0x6492...6492`,
`src/lib/erc6492.ts`) que viem sait detecter nativement via son
"Universal Signature Validator" pour verifier la signature malgre l absence
de contrat deploye.

Cote demo, la detection du type de cle a appliquer se fait apres la
signature plutot qu avant (`getKeyTypeForSignature`,
`useAssociationSigning.ts`) : d abord verifier si la signature porte le
suffixe magique ERC-6492 (cas le plus specifique), sinon verifier si
l adresse a du bytecode deploye on-chain (`isSmartContract`, via
`getCode`), et sinon retomber sur K1 par defaut. Cette sequence refleve
directement l ordre de priorite impose par la nature des trois cas : un
suffixe magique est une preuve certaine, la presence de bytecode est une
preuve directe, et l absence des deux signale une adresse a cle privee
standard.
