# Speech Stack

This document describes the speech processing infrastructure including Speech-to-Text (STT), Text-to-Speech (TTS), and audio processing for Arabic and English.

## Overview

The Speech Stack provides:
- Real-time Speech-to-Text conversion for Arabic and English
- High-quality Text-to-Speech synthesis
- Audio preprocessing and noise reduction
- Latency optimization for interactive learning
- Cost-effective cloud integration

## Architecture

```python
class SpeechStack:
    def __init__(self):
        self.stt_services = {
            'google': GoogleSpeechToText(),
            'azure': AzureSpeechServices(),
            'whisper': WhisperSTT()
        }
        self.tts_services = {
            'google': GoogleTextToSpeech(),
            'azure': AzureTTS(),
            'elevenlabs': ElevenLabsTTS()
        }
        self.audio_processor = AudioProcessor()
```

## Speech-to-Text (STT)

### Multi-Provider Setup

```python
class STTRouter:
    def __init__(self):
        self.providers = {
            'google': {
                'languages': ['ar-EG', 'en-US'],
                'cost_per_minute': 0.024,
                'latency_avg': 2.1,
                'accuracy': {'ar': 0.87, 'en': 0.95}
            },
            'azure': {
                'languages': ['ar-EG', 'en-US'],
                'cost_per_minute': 0.020,
                'latency_avg': 2.5,
                'accuracy': {'ar': 0.85, 'en': 0.94}
            },
            'whisper': {
                'languages': ['ar', 'en'],
                'cost_per_minute': 0.006,
                'latency_avg': 4.2,
                'accuracy': {'ar': 0.82, 'en': 0.91}
            }
        }
    
    def transcribe(self, audio_data: bytes, language: str = 'ar') -> TranscriptionResult:
        # Route to optimal provider based on requirements
        provider = self.select_provider(language, audio_data)
        return self.providers[provider].transcribe(audio_data, language)
    
    def select_provider(self, language: str, audio_data: bytes) -> str:
        audio_duration = self.estimate_duration(audio_data)
        
        # For short audio (<10s), prioritize latency
        if audio_duration < 10:
            return 'google'  # Fastest response
        # For longer audio, prioritize cost
        else:
            return 'whisper'  # Most cost-effective
```

### Real-time Processing

```python
class RealTimeSTT:
    def __init__(self):
        self.stream_config = {
            'sample_rate': 16000,
            'chunk_duration': 0.5,  # 500ms chunks
            'language_detection': True,
            'interim_results': True
        }
    
    async def stream_transcribe(self, audio_stream) -> AsyncGenerator[str]:
        buffer = AudioBuffer()
        
        async for chunk in audio_stream:
            buffer.add_chunk(chunk)
            
            if buffer.duration >= self.stream_config['chunk_duration']:
                # Process accumulated audio
                result = await self.process_chunk(buffer.get_audio())
                
                if result.is_final:
                    yield result.transcript
                
                buffer.reset()
    
    async def process_chunk(self, audio_chunk: bytes) -> STTResult:
        # Language detection for mixed conversations
        detected_lang = self.detect_language(audio_chunk)
        
        # Transcribe with appropriate model
        transcript = await self.transcribe_async(audio_chunk, detected_lang)
        
        return STTResult(
            transcript=transcript,
            language=detected_lang,
            confidence=transcript.confidence,
            is_final=transcript.is_final
        )
```

### Arabic STT Optimization

```python
class ArabicSTTProcessor:
    def __init__(self):
        self.dialect_detector = DialectDetector()
        self.noise_reducer = NoiseReducer()
    
    def preprocess_arabic_audio(self, audio: bytes) -> bytes:
        # Detect Egyptian dialect vs MSA
        dialect = self.dialect_detector.detect(audio)
        
        # Apply dialect-specific preprocessing
        if dialect == 'egyptian':
            audio = self.normalize_egyptian_phonemes(audio)
        
        # Reduce background noise
        audio = self.noise_reducer.reduce(audio)
        
        return audio
    
    def post_process_transcript(self, transcript: str) -> str:
        # Normalize Arabic text
        normalized = self.normalize_arabic_text(transcript)
        
        # Add punctuation for better readability
        punctuated = self.add_arabic_punctuation(normalized)
        
        return punctuated
```

## Text-to-Speech (TTS)

### Voice Selection

```python
class TTSVoiceManager:
    def __init__(self):
        self.voices = {
            'arabic': {
                'male': {
                    'provider': 'google',
                    'voice_id': 'ar-EG-Wavenet-D',
                    'quality': 'high',
                    'cost_per_char': 0.000016
                },
                'female': {
                    'provider': 'azure',
                    'voice_id': 'ar-EG-SalmaNeural',
                    'quality': 'high',
                    'cost_per_char': 0.000020
                }
            },
            'english': {
                'male': {
                    'provider': 'elevenlabs',
                    'voice_id': 'teacher_male_us',
                    'quality': 'premium',
                    'cost_per_char': 0.000300
                },
                'female': {
                    'provider': 'google',
                    'voice_id': 'en-US-Wavenet-F',
                    'quality': 'high',
                    'cost_per_char': 0.000016
                }
            }
        }
    
    def synthesize(self, text: str, language: str, voice_type: str = 'female') -> bytes:
        voice_config = self.voices[language][voice_type]
        provider = voice_config['provider']
        
        audio_data = self.tts_providers[provider].synthesize(
            text=text,
            voice_id=voice_config['voice_id'],
            speed=1.0,
            pitch=0
        )
        
        return self.optimize_audio_quality(audio_data)
```

```