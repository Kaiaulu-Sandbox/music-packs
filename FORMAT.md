# The music pack format

A music pack is a folder of looping music that a game can play. The format is open and small, so any tool can make a pack and any game can play one. The game and the tool that made a pack share only this format.

## What is in a pack

```
my-pack/
  pack.json        the list of tracks
  loops/
    trap.mp3       one audio file per track
    lofi.mp3
```

## pack.json

```json
{
  "format": "music-pack",
  "version": 1,
  "name": "Genre loops",
  "about": "63 genre loops, from slow blues at 62 BPM to thrash metal at 190.",
  "author": "Kaiāulu Sandbox",
  "license": "All rights reserved. Free to play in Kaiāulu Sandbox games.",
  "tracks": [
    {
      "id": "trap",
      "title": "Trap",
      "bpm": 140,
      "family": "Hip-hop",
      "file": "loops/trap.mp3",
      "loop": { "start": 0.25, "length": 3.428571 }
    }
  ]
}
```

| Field | Required | Rules |
|---|---|---|
| `format` | yes | The text `music-pack`. |
| `version` | yes | The number `1`. |
| `name` | yes | 1 to 60 characters. |
| `about` | | Up to 200 characters. |
| `author` | | Up to 60 characters. |
| `license` | yes | Up to 200 characters. It says how others may use the music. |
| `tracks` | yes | 1 to 200 tracks. |

Each track:

| Field | Required | Rules |
|---|---|---|
| `id` | yes | 1 to 40 characters from `a-z`, `0-9`, and `-`. Each id appears once in the pack. |
| `title` | yes | 1 to 60 characters. Players see it. |
| `bpm` | yes | A number from 40 to 240: the tempo the loop plays at. |
| `file` | yes | The audio file's path inside the pack, with `/` between folders, 200 characters or shorter. It stays inside the pack: it never starts with `/`, never holds `..`, and never names a web address. Each folder and file name starts and ends without a space, and leaves out `\`, `:`, `?`, `#`, `%`, `*`, `"`, `<`, `>`, and the vertical bar; spaces inside a name, accents, and parentheses are fine. It ends in `.mp3`, `.m4a`, `.ogg`, `.wav`, or `.flac`. |
| `loop` | | `start` and `length` in seconds. The player repeats that stretch of the file. Without it, the whole file repeats. |
| `family` | | Up to 40 characters, such as a genre family. |
| `credit` | | Up to 200 characters. The player shows it while the track plays, for music whose license asks for a credit. |

A player reads the fields it knows and skips the rest, so later versions can add fields. Every text field is shown as plain text.

## Audio

- **Format:** MP3 plays everywhere, so it is the safe choice: 44.1 kHz, stereo, about 160 kbps. A game may stream fewer kinds of file than the format names: Kaiāulu Sandbox's first game streams MP3 and plain WAV today (PCM of 8, 16, 24, or 32 bits, or float of 32 or 64 bits, at a sample rate the browser plays; 8,000 to 96,000 Hz plays everywhere), and turns the other kinds away until it can measure them (see Safety and privacy). An MP3 opens with its ID3 tags, any padding of zero bytes, and then its first frame.
- **Size:** each file is 4 MB or smaller, and each loop is 90 seconds or shorter. A whole file, with its lead-in and tail, holds 120 seconds of audio or less, in one or two channels. `pack.json` is 256 KB or smaller.
- **Loudness:** about −18 dBFS RMS, with peaks at or under −1 dBFS, so every track in every pack plays at a similar volume. A game may turn a louder loop down: that game plays a loop above −16 dBFS RMS at −16, and a loop whose loudest 0.4 seconds sit above −10 dBFS RMS comes down until they sit at −10, so a loud burst inside a quiet loop is turned down too.
- **A clean loop:** give the file at least 0.25 s of lead-in before `loop.start` and at least 0.25 s after the loop's end, each copied from the loop itself (the lead-in is the loop's ending, and the tail is the loop's beginning). MP3 decoders add a few hundredths of a second of padding, and this margin keeps the loop exact on every browser. The player crossfades the last 10 ms into the lead-in.
- **Length:** a whole number of bars at the track's `bpm`. Longer loops, of 8 bars or more, stay fresh longer.

## How a game uses a pack

- **Tempo groups.** The game sorts tracks by `bpm` into its own groups. Kaiāulu Sandbox's first game has five, one per difficulty: 62–99 (Personal and the tutorial), 100–119 (Home), 120–131 (Small office), 132–149 (School), and 150–190 (Clinic). A track below 62 joins the first group, and a track above 190 joins the last. A group with no track borrows from the nearest group that has one.
- **Choosing.** By default the game picks a track at random from the level's group, and skips the one it played last. The player can also pick any track.
- **A pack author never names a difficulty.** The tempo is enough.

## Where packs come from

1. **Built into the game.** A small starter pack ships inside the game and plays offline.
2. **Online, from GitHub.** A pack published in a public GitHub repository has an address: `owner/repo`, or `owner/repo/folder` when the pack sits in a folder. A version can follow the repository: `owner/repo@v2/folder`. An address is 200 characters or shorter, and a pack sits up to 8 folders deep. The owner holds letters, digits, and hyphens, up to 39 characters; the repository, the version, and each folder hold letters, digits, dots, underscores, and hyphens, up to 100 characters each. (A track's `file` may hold spaces and accents; the folders in a pack's address keep to the plainer set.)
   - The game asks GitHub first: `https://raw.githubusercontent.com/{owner}/{repo}/{version or HEAD}/{folder}/pack.json`.
   - If that fails, it asks jsDelivr, a free service that serves the same GitHub files: `https://cdn.jsdelivr.net/gh/{owner}/{repo}@{version}/{folder}/pack.json` (without `@{version}` when none is given).
   - Audio files load the same way, one track at a time, as each is needed.
3. **From the player's device** (planned). A downloaded pack plays offline with zero requests.

## Safety and privacy

- **Data only.** A pack holds JSON and audio. The game reads `pack.json` as data, checks every field against the rules above, and decodes audio with the browser's audio decoder (an MP3) or with its own sample reader (a WAV). Nothing in a pack runs as code.
- **Measured before decoding.** A small audio file can decode to hours of sound. The game reads a streamed file's length from the file's own bytes first, and takes only a file in one or two channels that measures inside its kind's limit:
  - **An MP3** goes to the browser's decoder when it measures 240 seconds or less, and it goes from its first frame on (the tags and padding before that frame are cut off), so the decoder sees only bytes the game measured. An MP3's measure is the most audio its bytes could hold, and real music reads long (up to about 1.5 times its length at 256 and 320 kbps), so this limit sits at twice the format's.
  - **A WAV** that measures 150 seconds or less is read by the game's own code, sample by sample as its header describes. A browser's decoder goes by what a file's bytes look like, whatever its header says, so the game hands it MP3 alone.
  - Once a file is decoded, its true length is held to the 120-second limit above.
- **Text a player can see.** The game shows a pack's text as plain text, and leaves out characters that steer the reading direction, characters that take no room on the screen, and accents stacked more than three deep.
- **Two hosts.** The game's security policy allows requests to `raw.githubusercontent.com` and `cdn.jsdelivr.net` and to nowhere else, over HTTPS, without cookies and without a referrer.
- **What the host sees.** When a pack streams, GitHub or jsDelivr sees the player's connection, as any website does. The pack's author sees nothing. The game sends no account, cookie, or personal information.
- **The player decides.** The game asks nothing of the internet until the player chooses online music. Built-in music and "My own music" make zero requests, and leaving online music ends any request under way.

## Making a pack

1. Make each loop a whole number of bars at a steady tempo.
2. Export each as an MP3 with the lead-in and tail described under Audio.
3. Write `pack.json` with each track's `id`, `title`, `bpm`, `file`, and `loop`.
4. Publish the folder in a public GitHub repository, and share its address.
