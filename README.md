# SWCCG card art

The printed artwork of every Star Wars CCG card, cut from the card scans, plus an AI-upscaled copy of each, with a
manifest that records where every crop came from and how it was cut. Built for
[swccg-cardmaker](https://github.com/billbisco/swccg-cardmaker) (its gallery loads these files) and for the
`Set/Thumbs` pages on [wiki.swccg.com](https://wiki.swccg.com).

## Layout

| Path | What |
|---|---|
| `decipher/orig/<gempId>.png` | The art window cut from the printed card's scan — untouched pixels (the reference) |
| `decipher/x4/<gempId>.webp` | The same crop upscaled 4× with RealESRGAN x4plus (an enhancement: it invents fine detail) |
| `decipher/manifest.json` | Per card: title, side, set, scan source, crop box, frame family, status |

Cards are keyed by their GEMP id (`5_75` = set 5, card 75).

Load a file from the CDN: `https://cdn.jsdelivr.net/gh/billbisco/swccg-card-art@main/decipher/x4/5_75.webp`.

## How the crops are made

- **Source scan.** Decipher's 350 px scans (as served by the Players Committee). For cards the Committee has
  re-rendered with errata, the printed face is used instead (the wiki's Decipher-archive copy, or the Committee's
  2021 2× enlargement of the same scan), because the re-renders regrade and reframe the art.
- **Normalise.** Each scan is reduced to the card itself at 350×490 (490×350 landscape), so 2× copies and bordered
  archive copies match the plain scans.
- **Window.** Cards are grouped by frame (side, card type, character class, era, orientation). Each scan is aligned
  to a reference scan of its group (OpenCV ECC, affine); the art window is where the aligned scans differ (the
  frame is identical, the art is not). The window is then mapped back into every scan. Systems are full-art cards:
  the picture is cut between their two bars. Every crop is trimmed 3 px inside the window; leftover keylines and frame
  bands at an edge are peeled off. Windows that are not rectangles (Objective cut corners, Reflections II combo rounded
  corners) keep their shape: the frame's corners are transparent in `orig/` and `x4/`. Scans that do not align, and special layouts (Admiral's Orders, Epic Events, Jedi Tests),
  are marked in the manifest instead of being guessed.
- **Upscale.** RealESRGAN x4plus (the `realesrgan-x4plus` ncnn model) run with upscayl-bin on an NVIDIA GPU, saved
  as WebP quality 92. Re-running a better upscaler later only replaces `x4/`.
- **Scans** are not stored here; each card's `scan_source` in the manifest links the exact file the crop was cut from.

Tooling: `art_library_all.py` in swccg-cardmaker.

## Rights

Card art © Lucasfilm Ltd.; card designs © Decipher Inc. This repository is a fan reference for the Star Wars CCG
community and grants no licence to the images. Code and the manifest format are MIT.
