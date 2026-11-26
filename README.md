# AMI headset recordings, short windows

This repository holds short windows of 23 meetings of the AMI Meeting Corpus, cut from the
individual headset microphone recordings, together with the reference speaker turns of the
same windows. It exists so that a build can fetch a small, fixed part of the corpus from one
place.

Nothing was resampled, mixed, filtered or edited. Each window is the exact run of samples the
publisher serves for that part of the recording.

## What is here

- `audio/<meeting>_<start>_<end>.flac`: one multi-channel FLAC per window (16 kHz, 16 bit,
  lossless). Channel k is `Headset-k` of that meeting, so a file has four or five channels.
  `<start>` and `<end>` are seconds from the start of the meeting recording.
- `rttm/<meeting>_<start>_<end>.rttm`: the reference turns of the window, clipped to it, with
  times counted from the start of the window.
- `MANIFEST.json`: for every window and every channel, the publisher URL, the first and last
  byte of the range that was fetched, and the sha256 of those bytes. Decoding a channel of a
  FLAC file to 16-bit little-endian PCM gives exactly those bytes.
- `SHA256SUMS`: the digest of every file under `audio/` and `rttm/`.

## Source and licence

- Audio: AMI Meeting Corpus, individual headset microphones, as published at
  https://groups.inf.ed.ac.uk/ami/corpus/ (files
  `amicorpus/<meeting>/audio/<meeting>.Headset-<k>.wav`).
  J. Carletta et al., "The AMI meeting corpus: A pre-announcement", MLMI 2005.
- Licence of the corpus: Creative Commons Attribution 4.0 International (CC BY 4.0). The
  corpus belongs to the AMI consortium; this repository only mirrors small parts of it.
- Reference turns: the `only_words` references of the AMI diarization setup
  (https://github.com/pyannote/AMI-diarization-setup, a fork of the BUT Speech@FIT setup,
  Apache License 2.0), derived from the manual annotations of the corpus, version 1.6.2.
  F. Landini, J. Profant, M. Diez, L. Burget, "Bayesian HMM clustering of x-vector sequences
  (VBx) in speaker diarization: theory, implementation and analysis on standard tasks".

## Check a window against the publisher

For channel k of a window, `MANIFEST.json` gives `url`, `first_byte`, `last_byte` and
`sha256`. Fetch that byte range from the publisher and hash it:

```
curl -s -r <first_byte>-<last_byte> <url> | sha256sum
```

The result equals the listed `sha256`, and it equals the sha256 of channel k of the FLAC
file decoded to 16-bit little-endian PCM.
