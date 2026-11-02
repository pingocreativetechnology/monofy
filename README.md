# monofy

Fix audio recordings where only one channel has sound. A common case is a mic recorded into the left channel of a stereo or multichannel file.

`monofy` finds the channel that has the audio, copies it to both left and right as a stereo file, trims the silence at the start and end, normalises the loudness, and exports a WAV that's ready for DaVinci Resolve or any other editor. It can also export an MP3.

## What it does

1. **Finds the channel with sound.** It measures the RMS level of every channel and keeps the loudest, so it works with stereo, 4-channel, or any other layout.
2. **Makes it stereo** by copying that channel to both left and right, so the silent channels aren't mixed in.
3. **Trims silence** at the start and end (below -50 dB by default), keeping 0.1 s of padding on each side.
4. **Normalises the volume** using two-pass EBU R128 (`loudnorm`) to -16 LUFS by default, with true peak at -1.5 dBTP.
5. **Exports** a 48 kHz / 24-bit WAV, a 320 kbps MP3, or both.

## Install

Requires [ffmpeg](https://ffmpeg.org) (`brew install ffmpeg` on macOS).

```bash
curl -fsSL https://raw.githubusercontent.com/pingocreativetechnology/monofy/main/monofy -o ~/.local/bin/monofy
chmod +x ~/.local/bin/monofy
```

Any directory on your `PATH` will do instead of `~/.local/bin`.

## Usage

```bash
monofy song.m4a              # → song_stereo.wav
monofy -b song.m4a           # → WAV + MP3
monofy -m song.m4a           # → MP3 only
monofy -b *.m4a              # batch
monofy -k song.m4a           # don't trim silence
monofy -t -40 song.m4a       # trim more aggressively (noisy room)
monofy -l -14 song.m4a       # custom loudness target (LUFS)
monofy -c 2 song.m4a         # force channel 2 instead of auto-detecting
```

| Flag | Meaning |
|------|---------|
| `-m` | MP3 only |
| `-b` | WAV and MP3 |
| `-k` | Keep silence (don't trim the start and end) |
| `-t dB` | Silence threshold for trimming. Default `-50`. Raise it (e.g. `-40`) if background noise stops the trim from working. |
| `-l LUFS` | Loudness target. Default `-16`. Use `-23` for broadcast or `-14` for loud streaming. |
| `-c N` | Use channel `N` (1-based) instead of the loudest |

Output files are written next to the input as `<name>_stereo.wav` / `<name>_stereo.mp3`. Any input format ffmpeg can read works, not just `.m4a`.

## License

MIT
