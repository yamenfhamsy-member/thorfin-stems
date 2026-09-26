# thorfin-stems

Free cloud stem separation backend for Thorfin Audio, running on GitHub
Actions (public repo = unlimited free runner minutes).

## How it works

1. The app uploads audio to catbox.moe (keyless) and triggers `separate.yml`
   via `workflow_dispatch` with `{audio_url, job_id, ext}`.
2. The runner installs CPU torch + Demucs (pip cache), downloads the audio,
   extracts audio from video containers with ffmpeg if needed, and runs
   `htdemucs --two-stems vocals`.
3. `vocals.wav` + `instrumental.wav` are uploaded as the `stems-<job_id>`
   artifact (7-day retention). The app polls, downloads, and deletes it.

## Trigger

```bash
curl -X POST https://api.github.com/repos/yamenfhamsy-member/thorfin-stems/actions/workflows/separate.yml/dispatches \
  -H "Authorization: Bearer $PAT" \
  -H "Accept: application/vnd.github+json" \
  -d '{"ref":"main","inputs":{"audio_url":"https://…","job_id":"abc123","ext":"mp3"}}'
```

The PAT needs Actions read/write on this repo.
