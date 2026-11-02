---
name: transcribe-audio
description: Transcribe audio or video files (meetings, interviews, podcasts, lectures, voice notes) into text and SRT subtitles with the Ears tools. Use when the user shares an audio or video file or a link to one and wants a transcript, subtitles, minutes, a summary, action items or quotes.
---

# Transcribe audio and video with Ears

Ears is a remote tool server at https://useears.com. It receives the audio or video you point it to, returns the transcript, the detected language and SRT subtitles, and does not store the file or the text. Cost: 1 credit per minute of audio (rounded up per file, minimum 1 credit).

## Steps

1. **Check the balance for long jobs.** Call `account`. If credits are short, give the user the `buy_credits_url` it returns and wait.
2. **A direct https link to a file** (mp3, wav, m4a, mp4...): call `transcribe` with `audio_url`. Add `language` (for example `es`) only if the user says which language it is. Pass `format: "srt"` or `"both"` only when the user wants subtitles; the default returns just the text, which keeps the answer short. Pages of YouTube, Instagram, TikTok and similar platforms are not supported: ask the user for a file they own or have the rights to.
3. **A file on the user's computer** (you can run shell commands): call `upload_link`, replace `/path/to/file.mp3` in the returned `curl` command with the real path and run it. The command uploads that one file to https://useears.com and prints the transcript as JSON. Say which file you are about to upload before you do it, and do not upload other files.
4. **Long recordings and files over 25 MB:** processing takes about 15 seconds per minute of audio, so a long call can hit your connection's timeout. If a recording is longer than about 10 minutes (or over 25 MB) and ffmpeg is installed, cut it into 5-minute parts: `ffmpeg -i input.mp4 -vn -ac 1 -ar 16000 -b:a 32k -f segment -segment_time 300 -reset_timestamps 1 part_%03d.mp3`. Upload each part in order and join the texts (for subtitles, add 5 minutes per part to every timestamp). If ffmpeg is not available, tell the user the limit.
5. **Use the result.** The JSON has `text` (and `srt` when requested), `language`, `minutes`, `credits_charged` and `credits_left`. Present what the user asked for (full transcript, summary, action items, quotes). For timestamps or subtitles, ask again with `format: "srt"` or `"both"` (for a local file, request a new upload link with that format). Offer to save subtitles to a `.srt` file.

## Notes

- The recording leaves the user's device and goes to https://useears.com for processing. If it sounds sensitive (medical, legal, confidential), confirm with the user before sending it.
- Transcripts come from automatic speech recognition and can contain errors, especially names, numbers and overlapping speakers. Say so when accuracy matters.
- A failed transcription is not charged. Do not retry the same file in a loop.
