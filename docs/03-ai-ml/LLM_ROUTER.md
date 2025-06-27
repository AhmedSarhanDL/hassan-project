# LLM Router

This document describes the intelligent routing system that optimizes LLM usage based on query complexity, cost, and performance requirements.

## Overview

The LLM Router is a critical component that:
- Routes queries to appropriate LLM models based on complexity
- Minimizes operational costs through intelligent model selection
- Ensures optimal response times for different query types
- Supports both Arabic and English language processing

## Routing Logic

### Query Classification

```python
class QueryClassifier:
    """Classifies incoming queries for optimal LLM routing"""
    
    def classify_query(self, query: str, context: dict) -> QueryClass:
        features = {
            'length': len(query.split()),
            'language': detect_language(query),
            'complexity': self.assess_complexity(query),
            'subject': context.get('subject'),
            'requires_calculation': self.has_math_operations(query),
            'requires_reasoning': self.needs_multi_step_reasoning(query)
        }
        
        if features['requires_calculation'] or features['complexity'] > 0.8:
            return QueryClass.COMPLEX
        elif features['length'] > 50 or features['requires_reasoning']:
            return QueryClass.MEDIUM
        else:
            return QueryClass.SIMPLE

    def assess_complexity(self, query: str) -> float:
        """Score query complexity from 0.0 to 1.0"""
        indicators = [
            ('explain', 0.6),
            ('analyze', 0.7),
            ('compare', 0.8),
            ('solve', 0.9),
            ('prove', 1.0),
            ('derive', 1.0)
        ]
        
        score = 0.0
        for keyword, weight in indicators:
            if keyword in query.lower():
                score = max(score, weight)
        
        return score
```

### Model Selection Matrix

| Query Type | Arabic | English | Cost/1K tokens | Latency Target |
|------------|---------|---------|----------------|----------------|
| Simple Q&A | Mixtral 8x7B | Mixtral 8x7B | $0.0005 | <2s |
| Complex Math | GPT-4 Turbo | GPT-4 Turbo | $0.01 | <8s |
| Reasoning | GPT-4o | GPT-4o | $0.005 | <6s |
| Code Generation | Claude 3.5 Sonnet | Claude 3.5 Sonnet | $0.003 | <5s |
| Translation | GPT-4o | GPT-4o | $0.005 | <3s |

### Routing Decision Engine

```python
class LLMRouter:
    """Main routing engine for LLM selection"""
    
    def __init__(self):
        self.cost_thresholds = {
            'student': 0.002,  # $0.002 per query max
            'teacher': 0.005,  # $0.005 per query max
            'admin': 0.01     # $0.01 per query max
        }
        
        self.models = {
            'mixtral_local': {
                'cost_per_1k_tokens': 0.0005,
                'avg_latency_ms': 1500,
                'supports_arabic': True,
                'max_context': 32000,
                'strengths': ['general_qa', 'simple_math']
            },
            'gpt4_turbo': {
                'cost_per_1k_tokens': 0.01,
                'avg_latency_ms': 3000,
                'supports_arabic': True,
                'max_context': 128000,
                'strengths': ['complex_math', 'reasoning', 'analysis']
            },
            'gpt4o': {
                'cost_per_1k_tokens': 0.005,
                'avg_latency_ms': 2500,
                'supports_arabic': True,
                'max_context': 128000,
                'strengths': ['translation', 'creative_writing', 'reasoning']
            }
        }
    
    def route_query(self, query: str, user_context: dict) -> RoutingDecision:
        """Route query to optimal model"""
        
        # Step 1: Classify query
        query_class = self.classifier.classify_query(query, user_context)
        
        # Step 2: Check user cost limits
        user_tier = user_context.get('tier', 'student')
        max_cost = self.cost_thresholds[user_tier]
        
        # Step 3: Select model based on requirements
        candidates = self.filter_models(query_class, max_cost, user_context)
        
        # Step 4: Choose optimal model
        selected_model = self.select_optimal_model(candidates, query_class)
        
        return RoutingDecision(
            model=selected_model,
            estimated_cost=self.estimate_cost(query, selected_model),
            estimated_latency=self.estimate_latency(query, selected_model),
            reasoning=f"Selected {selected_model} for {query_class} query"
        )
```

## Cost Optimization

### Cost Tracking

```python
class CostTracker:
    """Track and optimize LLM usage costs"""
    
    def track_usage(self, user_id: str, model: str, tokens_used: int):
        """Record usage for cost calculation"""
        cost = self.calculate_cost(model, tokens_used)
        
        usage_log = {
            'user_id': user_id,
            'model': model,
            'tokens_used': tokens_used,
            'cost_usd': cost,
            'timestamp': datetime.utcnow()
        }
        
        self.save_usage_log(usage_log)
        self.update_user_spending(user_id, cost)
    
    def calculate_cost(self, model: str, tokens: int) -> float:
        """Calculate cost based on model pricing"""
        model_info = self.models[model]
        return (tokens / 1000) * model_info['cost_per_1k_tokens']
    
    def check_budget_limit(self, user_id: str) -> bool:
        """Check if user is within budget limits"""
        current_spending = self.get_monthly_spending(user_id)
        user_limit = self.get_user_budget_limit(user_id)
        
        return current_spending < user_limit
```

### Budget Controls

```python
# User budget limits (monthly)
BUDGET_LIMITS = {
    'free_tier': 1.0,      # $1/month
    'student_tier': 5.0,   # $5/month
    'teacher_tier': 20.0,  # $20/month
    'school_tier': 100.0   # $100/month
}

def enforce_budget_limit(user_id: str, estimated_cost: float) -> bool:
    """Check if query would exceed budget"""
    current_spending = get_monthly_spending(user_id)
    user_tier = get_user_tier(user_id)
    limit = BUDGET_LIMITS[user_tier]
    
    return (current_spending + estimated_cost) <= limit
```

## Performance Optimization

### Caching Strategy

```python
class ResponseCache:
    """Cache responses for frequently asked questions"""
    
    def __init__(self):
        self.cache = {}
        self.ttl = 3600  # 1 hour cache
    
    def get_cache_key(self, query: str, context: dict) -> str:
        """Generate cache key from query and context"""
        context_hash = hashlib.md5(
            json.dumps(context, sort_keys=True).encode()
        ).hexdigest()[:8]
