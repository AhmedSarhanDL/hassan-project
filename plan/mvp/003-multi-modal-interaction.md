# Multi-Modal Interaction MVP

## Goal
Enable multi-modal interaction (text, speech, images) for the AI tutor.

## Features
- Chat interface with text and speech input/output
- Image upload for math/science questions
- Speech-to-text and text-to-speech integration
- Support for Arabic and English

## Architecture
- React frontend (TypeScript)
- FastAPI backend
- Google/Azure/Whisper for STT
- Google/Azure/ElevenLabs for TTS

## Acceptance Criteria
- [ ] Users can chat using text or speech
- [ ] Image upload and recognition works
- [ ] TTS and STT work for Arabic/English

## Tasks
1. Build chat UI with text/speech input
2. Integrate STT and TTS APIs
3. Add image upload and recognition
4. Test multi-modal flows

## See Also
- [Speech Stack](../../../docs/03-ai-ml/SPEECH_STACK.md)
- [LLM Router](../../../docs/03-ai-ml/LLM_ROUTER.md)

