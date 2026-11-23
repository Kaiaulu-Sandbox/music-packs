# Music packs

Looping music for Kaiāulu Sandbox games, and an open format anyone can use to make more.

- **[genres/](genres/)** is the default pack: 63 genre loops, from slow blues at 62 BPM to thrash metal at 190. Its address in a game's Sound menu is `Kaiaulu-Sandbox/music-packs/genres`.
- **[FORMAT.md](FORMAT.md)** describes the music pack format: one `pack.json` and a folder of loops.

## Play your own pack

1. Make your loops, and write a `pack.json` as FORMAT.md describes.
2. Publish the folder in a public GitHub repository.
3. In the game's Sound menu, choose online music and enter your pack's address, such as `your-name/your-repo`.

A pack holds only a list and audio. A game reads both as data, and nothing in a pack runs as code.

## Licenses

- The format and FORMAT.md are dedicated to the public domain (CC0 1.0), so any tool or game may use them.
- The loops in `genres/` are © Kaiāulu Sandbox, all rights reserved. They are free to play in Kaiāulu Sandbox games.
