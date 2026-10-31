# RayFlow AI - Wispr Flow Hackathon Build Report

## Summary

RayFlow AI is a voice-first video editing studio built for the Wispr Flow
hackathon. The product was developed through a conversational loop: I spoke
product ideas and creative constraints with Wispr Flow, asked voice agents to
turn those ideas into implementation plans, and reviewed the resulting UI and
behavior after each iteration.

The central idea is simple: video editing should feel like a creative
conversation without taking control away from the editor. A creator can speak
an intention, receive a bounded editing proposal, review it, and keep working
on the editable timeline.

## Build approach

Wispr Flow shaped the project at three levels:

1. **Product discovery:** voice prompts explored what a natural creative
   director should feel like and which editing decisions must remain visible.
2. **Implementation:** voice agents helped translate those decisions into React,
   FastAPI, timeline, caption, provider, and rendering work.
3. **Verification:** spoken scenarios were turned into repeatable checks for
   mobile layout, voice input, editor actions, persistence, and MP4 export.

Representative prompts included:

- “Turn this into a calm cinematic reel with a clear approval step.”
- “Let me direct the selected clip by voice, but keep the proposed change visible.”
- “Check the editor after a mobile resize and preserve the timeline controls.”
- “Make the preview and exported MP4 use the same caption layout.”

## Implemented product surface

- React 19/Vite editor with a vertical preview, media controls, timeline, and
  responsive studio layout.
- FastAPI backend with workspace sessions, persistence, provider connections,
  AI Director planning, captions, and rendering routes.
- Voice direction through browser speech recognition with visible listening,
  interim transcript, stop behavior, permissions errors, and text fallback.
- AI Director proposals validated against supported editing actions before a
  user can apply them.
- Timeline editing for clips, captions, audio, titles, transitions, transforms,
  undo/redo, and Auto Reel templates.
- Local Librosa beat analysis, optional Faster-Whisper captions, and FFmpeg MP4
  rendering locally or through Cloud Run.
- Production deployment assets for Cloud Run services, a render job, private
  Cloud Storage, Secret Manager, and Artifact Registry.

## Verification record

The repository includes focused checks for provider isolation, action validation,
voice editing, browser behavior, caption rendering, timeline history, storage,
and real FFmpeg output. The most important acceptance rule is that an assistant
response is never treated as an applied edit until the validated plan and
editable timeline have changed.

The checked workflows include:

- Voice commands that rotate or move a selected clip.
- Review and approval of AI Director proposals.
- Caption selection, editing, import/export, and consistent preview rendering.
- Undo/redo after clip, audio, and caption changes.
- Responsive editor behavior on desktop and mobile layouts.
- Real MP4 rendering and download through the local pipeline.

Provider generation and physical microphone recognition remain environment-
dependent. Automated checks use provider mocks and simulated browser speech
where appropriate; they do not claim a live provider response without a real
credential and a configured device.

## Architecture decisions

- Keep voice as an input mode, not a separate editor. Spoken directions and
  typed directions resolve into the same validated action path.
- Keep approval explicit. The assistant proposes; the creator decides.
- Keep the timeline authoritative. Preview, state, and export derive from the
  same editable plan.
- Keep provider credentials server-side, encrypted per workspace, and absent
  from frontend bundles, URLs, and browser storage.
- Keep local development useful without paid services. Manual editing and
  rendering work with local storage and FFmpeg.

## Deployment readiness

Local development is documented in [docs/deployment.md](docs/deployment.md).
Cloud Run deployment is documented in
[GCP_CLOUD_RUN_DEPLOYMENT.md](GCP_CLOUD_RUN_DEPLOYMENT.md). No credentials are
committed, and no deployment should be considered complete until the deployed
frontend, backend readiness endpoint, render job, and a short exported reel have
been checked together.

## Closing note

Wispr Flow was part of the development environment, not just the project
headline. It made it possible to move fluidly between “what should this feel
like?”, “how should the agent behave?”, and “does the finished editor actually
work?”. RayFlow is the resulting experiment in building a serious creative tool
through voice.
