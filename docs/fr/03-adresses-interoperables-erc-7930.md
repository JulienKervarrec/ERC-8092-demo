# Chapitre 3 -- Adresses interoperables : pourquoi ERC-7930 plutot qu une adresse brute

Une des dependances explicites d ERC-8092 est ERC-7930 (Interoperable
Addresses), qui resout un probleme different mais complementaire : un compte
`initiator` ou `approver` n est pas necessairement une adresse EVM de 20
octets. Il pourrait s agir d un compte sur une chaine non-EVM, avec un format
d adresse totalement different. Pour rester agnostique vis-a-vis du type de
chaine, ERC-8092 exige que ces champs soient encodes en representation
binaire ERC-7930 plutot qu en adresses brutes -- une decision de conception
qui elargit deliberement le perimetre du standard au-dela du seul ecosysteme
EVM.

Dans la demo, cette conversion est geree par la librairie externe
`@wonderland/interop-addresses`, invoquee par la fonction utilitaire
`addressToErc7930` (`src/lib/types.ts`), qui prend une adresse EVM classique
et un `chainId`, et produit la representation ERC-7930 correspondante
(version 1, type de chaine `eip155`, reference de chaine egale au `chainId`
encode en hexadecimal). A l inverse, `extractAddress`
(`src/lib/erc7930.ts`) fait le chemin retour : pour une chaine EVM, la
representation ERC-7930 se termine toujours par les vingt derniers octets
qui constituent l adresse, donc extraire l adresse revient simplement a
prendre les quarante derniers caracteres hexadecimaux de la charge utile.

Cette implementation reste volontairement minimale -- elle ne gere que le cas
EVM, en s appuyant sur une propriete structurelle du format (le suffixe fixe
de 20 octets) plutot que de decoder integralement l enveloppe ERC-7930
generale. C est un choix pragmatique justifie par le fait que la demo elle-
meme ne manipule que des comptes EVM, meme si le standard sous-jacent est
concu pour aller au-dela.
