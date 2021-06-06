In **multiply mode** [BIP-38](https://github.com/bitcoin/bips/blob/master/bip-0038.mediawiki) defines three new artifacts:

* `intermediate code`
* `confirmation code`
* `encrypted private key`

It also makes use of existing standard artifacts:

* `private key`
* `public key`
* `payment address`

The multiply mode scenario envisions two actors, an `owner` and a `printer`:

 1. The owner creates an `intermediate code` using a secret passphrase (on a trusted platform).
 2. The owner provides the `intermediate code` to the printer.
 3. The printer generates an `encrypted private key` from the `intermediate code`.
 4. The printer provides the `encrypted private key` and a `confirmation code` to the owner.
 5. The printer has access to the `public key` and `payment address` and may provide these to the owner as well.
 6. The printer has no ability to obtain the corresponding `private key` without the original passphrase.
 7. The owner validates information obtained from the printer using the passphrase (more below).

### Privacy
From a privacy standpoint the only material issue is that the owner has given the printer knowledge of the `public key` of an owner `private key`. This means that any payment to a `payment address` derived from that `public key` can be associated with the owner's person (i.e. tainted). The printer is therefore trusted to not disclose this association.

### Security
The owner must validate information returned from the printer. Otherwise there are several ways the printer can steal (i.e. intentionally) or lose (i.e. through negligence) the owner's money. Validation must be done on a trusted platform, such as the one first used to generate the `intermediate code`. Otherwise the `private key` of the `encrypted private key` may have been compromised.

### Theft of Money
The printer could just generate an `encrypted private key` without using the owner's `intermediate code`. In this case any money spent to the `public key` of the `encrypted private key` would be spendable by the printer and not by the owner. The owner can prevent this by validating the `confirmation code` against the originating `intermediate code`.

The printer could return a `payment address` (or `public key`) that is unrelated to the `intermediate code`. If the `intermediate code` is valid but does not represent the corresponding `payment address` any money sent to the `payment address` will be spendable by the printer and not the owner. Therefore the validation step must accept the `payment address` as a parameter.

### Denial of Money
The printer could generate a valid `confirmation code` using the owner's `intermediate code` and return an unrelated `encrypted private key`. If the owner validates the `confirmation code` against the `intermediate code` the owner will know that the printer cannot spend money sent to the related `payment address`. However this offers no assurance whatsoever that the owner can spend from the validated `payment address`.

### Observations
In all respects the `confirmation code` is actually a public analog to the `encrypted private key`. In other words it retains the encrypted `public key` of the `private key` that is encrypted within the `encrypted private key`. As such, using the original passphrase, only the owner can decrypt the `confirmation code`. However the printer has access to its `public key` as well, so this offers no protection against the printer.

The `confirmation code` cannot be used to validate the `encrypted private key`. Protecting against denial of money by the printer requires the `owner` to validate the `encrypted private key` provided by the printer against the `payment address` using the original passphrase. This is the only way the owner protects against both theft and denial of money.

### Relevant BIP-38 Statements
> ...if the `payment address` can be recreated by decrypting its `encrypted private key` with a passphrase, and it's a strong passphrase one can be certain only he knows himself, then he can safely conclude that nobody could know the `private key` to that `payment address`.

In other words, the `confirmation code` is NOT used to validate the owner's ability to spend from the `payment address` using the `encrypted private key`.

> The party generating the `payment address` has the option to return a `confirmation code` back to owner which allows owner to independently verify that he has been given a `payment address` that actually depends on his passphrase, and to confirm the lot and sequence numbers (if applicable). This protects owner from being given a `payment address` by the second party that is unrelated to the key derivation and possibly spendable by the second party. If a `payment address` given to owner can be successfully regenerated through the confirmation process, owner can be reasonably assured that any spending without the passphrase is infeasible.

The "confirmation process" refers to use of the `confirmation code` by the owner to validate that the `payment address` is associated to the owner-generated `intermediate code`. Note however that the guarantee provided is only against theft of money, not against denial: *"any spending without the passphrase is infeasible"*.

### Terminology
The name `confirmation code` is misleading from a security standpoint and complicates an understanding of **multiply mode**. The value is actually a `public key` derived from the `private key` of the `encrypted private key` and encrypted for the owner. For this reason libbitcoin refers to this artifact as the `encrypted public key`.

In the name `intermediate code` the term "intermediate" is vague, as there are several steps and artifacts in the scenario. The term "code" does not refine "intermediate" as all of the artifacts are codes of some sort. In the interest of clarity and brevity libbitcoin refers to the `intermediate code` as a `token`.

### Recommendations
Given that the **multiply mode** scenario rests on the presumption that the owner cannot trust the printer, we conclude that there is no valid use case for the `confirmation code`. We strongly recommend against use of the `confirmation code` and that BIP-38 be modified to remove the [Confirmation Code](https://github.com/bitcoin/bips/blob/master/bip-0038.mediawiki#confirmation-code) section altogether.

The scenario should be:

 1. The owner creates a `intermediate code` using a secret passphrase.
 2. The owner provides the `intermediate code` to the printer.
 3. The printer generates an `encrypted private key` from the `intermediate code`.
 4. The printer provides the `encrypted private key` to the owner.
 5. The owner extracts the `payment address` from the `encrypted private key` using the passphrase.

 * Steps 1 and 5 **must** be carried out on a trusted platform by the owner.
 * The printer **will** have knowledge of the `public key` (and `payment address`).
 * Lot and sequence validation (as applicable) **must** use the `encrypted private key`.

### See Also
Bitcoin Explorer [key encryption commands](https://github.com/libbitcoin/libbitcoin-explorer/wiki/Key-Encryption-Commands)