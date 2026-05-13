# raw/transcripts/

Source transcripts for video and audio material ingested into this vault.

> [!warning] Fidelity caveat — auto-captions
> Most files here are **YouTube auto-generated captions** pulled via `yt-dlp --write-auto-sub`. They are useful as a faithful-enough record of what was *said*, but they are **not authoritative**:
>
> - Speech-recognition errors are common, especially on technical jargon, names, acronyms, numbers, and code.
> - Punctuation and sentence boundaries are heuristic.
> - Speaker identification is absent.
> - No timing metadata is preserved (we strip the VTT timestamps to get clean prose).
>
> When citing a specific claim, **verify against the original video** before treating it as load-bearing. Quote-marks in vault notes implies confidence in the wording — don't fabricate that confidence from a noisy transcript.

## Naming convention

`<short-slug>-<youtube-id>.txt` — e.g. `limits-of-ai-rblcolfmyfw.txt`.

The YouTube ID makes the original source unambiguously recoverable; the slug makes the file scannable in `ls`.

## How transcripts are produced

```bash
mkdir -p /tmp/yt && cd /tmp/yt
yt-dlp --skip-download --write-auto-sub --write-sub \
  --sub-langs "en.*,en" --sub-format vtt \
  -o "%(id)s.%(ext)s" "<youtube-url>"

# Then strip VTT chrome and inline timing tags into a single-paragraph .txt
```

Files in this directory are written by `AGENTS.md` operations (per [[../../AGENTS#4.1 INGEST]]). The LLM may add new transcript files here during ingest, but otherwise treats `raw/` as immutable per [[../../AGENTS#2. Directory layout]].
