# fuzzle-parts

Encrypted Fuzzle part bundles for the Fuzzle Revival app. The art belongs to the Fuzzle DAO.

The files here are public, but they are encrypted: each part has its own AES-256 key, and the app gets a key only
after the holder's wallet proves it owns a Fuzzle that uses that part. Without the key a file is random bytes.

## Layout
- One GitHub Release per build and part type, tag `<platform>-<build stamp>-<type>` (for example
  `android-stream-20261124o-groom`), at most 1,000 assets per release (a type with more gets `-2`, `-3`, ...).
- Each asset is `<bundle name>.fzp`.
- `index/index-<platform>-<stamp>.json` lists every part: release, asset, encrypted and plain size, sha256 of the file.

## File format FZP1
| bytes | content |
|---|---|
| 0..3 | `FZP1` |
| 4..19 | 16-byte nonce, the first AES-CTR counter block (big-endian, +1 per 16-byte block) |
| 20..end-32 | AES-256-CTR ciphertext of the Unity bundle |
| last 32 | HMAC-SHA256 over everything before it |

Per part a random 32-byte key K; the encryption key is HMAC-SHA256(K, "fzp1-enc"), the MAC key HMAC-SHA256(K, "fzp1-mac").
No key is stored in this repository or in the app.
