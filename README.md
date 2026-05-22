# Plexamp Music Formatter

A Python script that reads embedded tags written by [MusicBrainz Picard](https://picard.musicbrainz.org/) and reorganizes your music library into the folder and filename structure expected by Plex — ready for use with Plexamp.

---

## How It Works

Plex expects your music to be laid out like this:

```
Music/
  ArtistName/
    AlbumName/
      01 - Track Name.mp3
      02 - Track Name.mp3
  Various Artists/
    Compilation Album/
      01 - Track Name.mp3
```

For multi-disc albums, Plex uses a combined disc+track number format:

```
Music/
  Pink Floyd/
    The Wall/
      101 - In the Flesh.mp3
      102 - The Thin Ice.mp3
      201 - Hey You.mp3
```

This script reads the tags Picard has written to your files and automatically moves or copies them into that structure — no manual renaming needed.

> 📖 See the full Plex naming documentation: [Naming and Organizing Music Media](https://support.plex.tv/articles/categories/your-media/naming-and-organizing-music-media/)

---

## Prerequisites

- Python 3.7+
- [MusicBrainz Picard](https://picard.musicbrainz.org/) — used to tag your files before running this script
- `mutagen` — Python library for reading audio tags

Install dependencies with:

```bash
pip install mutagen
```

---

## Step 1 — Tag Your Files with MusicBrainz Picard

This script depends on accurate embedded tags. If your files aren't already tagged, do this first.

1. Download and install [MusicBrainz Picard](https://picard.musicbrainz.org/)
2. Drag your music folder into Picard
3. Click **Cluster** to group files by folder, then **Lookup** or **Scan** to fingerprint and identify them
4. Review the matches and accept them
5. Make sure **Save Tags** is enabled, then click **Save** to write the tags to your files

Picard will write the following tags that this script relies on: `albumartist`, `album`, `tracknumber`, `discnumber`, and `title`.

> 📖 Picard documentation: [MusicBrainz Picard User Guide](https://picard-docs.musicbrainz.org/en/usage/using_picard.html)

---

## Step 2 — Run the Script

### Dry Run (recommended first step)

Preview every move without touching any files:

```bash
python plex_music_organizer.py --source "/path/to/your/music" --dest "/path/to/PlexMusic" --dry-run
```

Check the generated `plex_organizer.log` file to confirm everything looks right before proceeding.

### Copy Mode (safe)

Copies files into the new structure while keeping your originals:

```bash
python plex_music_organizer.py --source "/path/to/your/music" --dest "/path/to/PlexMusic" --copy
```

### Move Mode

Moves files into the new structure:

```bash
python plex_music_organizer.py --source "/path/to/your/music" --dest "/path/to/PlexMusic"
```

> **Tip:** Use copy mode on your first real run so your originals are safe until you've confirmed Plex picks everything up correctly.

---

## What the Script Does with Your Tags

| Tag read from file | Used for |
|--------------------|----------|
| `albumartist` | Artist folder name |
| `album` | Album subfolder name |
| `tracknumber` | Track number prefix (e.g. `03`) |
| `discnumber` | Combined with track number for multi-disc albums (e.g. `302`) |
| `title` | Filename |

**Various Artists / Compilations** — if the `albumartist` tag is set to `Various Artists` (exactly as Picard writes it), those albums are placed under a `Various Artists/` folder as Plex requires.

> ⚠️ If you have compilation albums, make sure Picard has set the **Album Artist** tag to the literal string `Various Artists`, not the individual track artist. Plex is strict about this. See: [Handling Various Artists](https://support.plex.tv/articles/200265296-adding-music-media-from-folders/#toc-3)

---

## Logging

Every run saves a `plex_organizer.log` file next to the script. It records every file processed, where it was moved or copied to, any missing tags, and a summary at the end. Always check this after a dry run.

---

## Supported File Types

`.mp3` · `.flac` · `.m4a` · `.aac` · `.ogg` · `.opus` · `.wv` · `.ape` · `.wav` · `.aiff`

---

## After Running — Add Your Library to Plex

1. Open Plex and go to **Settings → Libraries → Add Library**
2. Choose **Music** and point it at your destination folder
3. Do **not** enable **Prefer local metadata** unless you have a specific reason to — Plex's online matching works better for most people

> 📖 More on this setting: [Identifying Music Using Embedded Metadata](https://support.plex.tv/articles/200265296-adding-music-media-from-folders/#toc-6)

---

## Notes

- The script scans subfolders recursively, so you can point `--source` at the root of your entire music library
- Files with naming conflicts at the destination are automatically renamed with a counter suffix rather than overwritten
- Empty source folders are not deleted after a move — remove them manually once you've verified everything looks correct in Plex

---

## Releases

Check the [Releases](../../releases) page for the latest stable version. It's recommended to download from there rather than pulling directly from the main branch, as main may contain work-in-progress changes.
