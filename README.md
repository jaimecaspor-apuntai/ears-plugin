# Ears: audio and video transcription for your AI assistant

Ears gives an AI assistant the ability to listen. Ask it to transcribe a meeting recording, an interview, a podcast episode or a voice note, and it returns the transcript, the detected language and SRT subtitles with timestamps. You pay as you go with prepaid credits: 1 credit = €0.01 = 1 minute of audio, credits never expire and there is no subscription. New accounts get 30 free credits.

## What the plugin does

- Adds the Ears remote MCP server (`https://useears.com/mcp`) with three tools: `account` (balance and a link to buy credits), `transcribe` (text, and optionally SRT subtitles, from a direct link to an audio or video file) and `upload_link` (a one-time command to upload a file from your computer).
- Adds the `transcribe-audio` skill, which tells the assistant when and how to use those tools.

## What leaves your machine

Only what you ask the assistant to transcribe. For a link, the Ears server downloads that file. For a local file, the assistant runs one `curl` command, shown to you, that uploads that single file to `https://useears.com`. The audio is processed in memory for speech recognition (Cloudflare Workers AI) and discarded; Ears stores no files and no transcripts. The plugin runs no other code, reads no credentials from your environment and sends nothing else anywhere.

## Set up

1. Install the plugin from this repository (`/plugin marketplace add jaimecaspor-apuntai/ears-plugin`, then `/plugin install ears@ears`), or add only the server: `claude mcp add --transport http ears https://useears.com/mcp`.
2. Run `/mcp` and sign in. Choose "Create a free account" and save the recovery key.
3. Ask: "Transcribe https://example.com/call.mp3 and list the action items."

Three things to try: "Transcribe https://useears.com/sample.wav and tell me who should receive the report", "Transcribe this meeting recording [link] and list the decisions and action items with owners", "Give me SRT subtitles for this talk [link]". The plugin only processes the file you point to; it never reads your conversation, your memory or the files attached to the chat.

Limits: 25 MB per file. Processing takes about 15 seconds per minute of audio, so recordings longer than about 10 minutes should be cut into 5-minute parts (`ffmpeg -i in.mp4 -vn -ac 1 -ar 16000 -b:a 32k -f segment -segment_time 300 -reset_timestamps 1 part_%03d.mp3`). Direct file links only: YouTube, Instagram and TikTok pages are not supported.

## Links

[Docs](https://useears.com/docs) · [Privacy](https://useears.com/privacy) · [Terms](https://useears.com/terms) · [Refunds](https://useears.com/refunds)

License: MIT (this plugin). The hosted service is covered by its own terms.
