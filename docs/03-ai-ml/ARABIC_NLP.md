# Arabic NLP Pipeline

This document describes the Arabic Natural Language Processing pipeline for tokenization, NER, sentiment analysis, and toxicity detection.

## Overview

The Arabic NLP pipeline provides:
- Arabic text tokenization and normalization using CAMeL Tools
- Named Entity Recognition for educational concepts
- Sentiment analysis and toxicity detection
- Support for Egyptian dialect variations

## Core Components

### Text Normalization

```python
class ArabicTextNormalizer:
    def __init__(self):
        self.camel_tools = CAMeLTools()
    
    def normalize(self, text: str) -> str:
        # Unicode normalization
        text = unicodedata.normalize('NFKC', text)
        
        # Remove diacritics
        text = re.sub(r'[\u064B-\u0652\u0640\u0670]', '', text)
        
        # Normalize Alef forms
        text = text.replace('
2', ' a7').replace(' a3', ' a7').replace(' a5', ' a7')
        
        # Normalize Teh Marbuta
        text = text.replace(' a9', ' 7')
        
        return text.strip()
```

### Named Entity Recognition

```python
class ArabicNER:
    def __init__(self):
        self.model = self.load_ner_model()
        self.entity_types = ['MATH_CONCEPT', 'PHYSICS_CONCEPT', 'PERSON', 'LOCATION']
    
    def extract_entities(self, text: str) -> List[Entity]:
        normalized_text = self.normalize_text(text)
        tokens = self.tokenize(normalized_text)
        predictions = self.model.predict(tokens)
        
        entities = []
        for token, label in zip(tokens, predictions):
            if label != 'O':
                entities.append(Entity(
                    text=token,
                    type=label.split('-')[1],
                    confidence=self.calculate_confidence(token, label)
                ))
        return entities
```

### Sentiment Analysis

```python
class ArabicSentimentAnalyzer:
    def analyze_sentiment(self, text: str) -> SentimentResult:
        processed_text = self.preprocess_for_sentiment(text)
        prediction = self.model.predict(processed_text)
        
        return SentimentResult(
            sentiment=prediction['label'],
            confidence=prediction['confidence'],
            emotions=self.analyze_emotions(text)
        )
```

### Toxicity Detection

```python
class ArabicToxicityDetector:
    def detect_toxicity(self, text: str) -> ToxicityResult:
        profanity_score = self.detect_profanity(text)
        hate_speech_score = self.detect_hate_speech(text)
        overall_score = max(profanity_score, hate_speech_score)
        
        return ToxicityResult(
            is_toxic=overall_score > 0.5,
            toxicity_score=overall_score,
            confidence=self.calculate_confidence(overall_score)
        )
```

## CAMeL Tools Integration

```python
# Setup
from camel_tools.tokenizers.word import simple_word_tokenize
from camel_tools.morphology.database import MorphologyDB
from camel_tools.morphology.analyzer import Analyzer

class ArabicTokenizer:
    def __init__(self):
        self.word_tokenizer = simple_word_tokenize
        self.morph_db = MorphologyDB.builtin_db()
        self.analyzer = Analyzer(self.morph_db)
    
    def tokenize(self, text: str) -> List[str]:
        return self.word_tokenizer(text)
```

## Performance Requirements

- Text normalization: <50ms per document
- NER: >85% F1 score on educational content
- Sentiment analysis: >80% accuracy
- Toxicity detection: <5% false positive rate

## Acceptance Criteria

- [ ] Arabic text normalization handles common variations
- [ ] NER extracts educational concepts accurately
- [ ] Toxicity detection filters inappropriate content
- [ ] Sentiment analysis works on student interactions
- [ ] Support for Egyptian dialect

## See Also
- [Graph RAG Pipeline](GRAPH_RAG.md)
- [LLM Router](LLM_ROUTER.md)
- [Speech Stack](SPEECH_STACK.md)

