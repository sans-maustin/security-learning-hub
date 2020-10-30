# GPG

## Objectives

We'll go over some examples on how to use and leverage gpg.

## GPG Examples

**a\) Create a key:**

```text
gpg --gen-key
```

**b\) Generate a Revoke Key:**

```text
gpg --output revoke.asc --gen-revoke
```

**c\) Create a key:**

```text
gpg --gen-key
```

**d\) Generate a Revoke Key:**

```text
gpg --output revoke.asc --gen-revoke
```

**e\) List All Keys and Private Keys**

```text
gpg --list-keys
gpg --list-secret-keys                    
```

**f\) Import and Export a public key into file public.key:**

```text
    gpg --import public.asc
    gpg --import key.gpg
    gpg --armor --export > public.asc
```

**g\) Import and Export a private key:**

```text
    gpg --allow-secret-key-import --import secring.gpg
    gpg --export-secret-keys > private.asc
```

**h\) Delete a public key \(from your public key ring\):**

```text
    gpg --delete-key "User Name"
```

**i\) Delete a private key \(a key on your private key ring\):**

```text
    gpg --delete-secret-key "User Name"
```

**j\) Verify fingerprint**

```text
    gpg --edit-key me@hi.com
```

**k\) Encrypt data:**

```text
    gpg -e -u "Sender User Name" -r "Receiver User Name" somefile
```

**l\) Encrypt data for multiple recipients**

```text
    gpg -e -r -r ... secret.txt
```

**m\) Decrypt Data:**

```text
    gpg -d mydata.tar.gpg
```

**i\) Sign cleartext save \*.asc**

```text
    gpg --clearsign doc.doc
```

