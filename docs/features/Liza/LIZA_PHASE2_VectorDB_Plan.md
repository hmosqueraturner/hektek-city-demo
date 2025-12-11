# LIZA Phase 2: Vector Database & Semantic Search Implementation

**Status**: BACKLOG - Foundation Ready  
**Priority**: HIGH (Future Development)  
**Created**: December 1, 2025  
**Phase 1 Dependency**: Lazy Loading Implementation

---

## 📋 Executive Summary

This document outlines the Phase 2 strategy for implementing Vector Database integration with Google AI Studio Pro to enable semantic search capabilities for LIZA. This will replace keyword-based matching with intelligent semantic understanding, allowing LIZA to process complex queries and understand context beyond simple keyword detection.

**Key Benefits**:
- 🎯 **Semantic Understanding**: "What did you do in banking?" → Finds all finance-related roles
- 📈 **Scalability**: Supports 100+ JSON sources without performance degradation
- 🚀 **Better Accuracy**: Context-aware retrieval vs. simple keyword matching
- 💰 **Google AI Pro**: Leverage existing premium membership for embeddings + File Search RAG

---

## 🎯 Objectives

### Primary Goals
1. **Replace keyword detection** with semantic similarity search using embeddings
2. **Integrate Google AI Studio Pro** embeddings API and File Search tool
3. **Maintain <2KB context size** while improving relevance scores
4. **Enable complex queries** that keyword matching cannot handle

### Success Criteria
- ✅ Semantic query accuracy >90% (vs. 70% keyword-based)
- ✅ Response latency <1.5s (including embedding generation)
- ✅ Context size remains 1-3KB average
- ✅ Seamless fallback to Phase 1 if Vector DB unavailable

---

## 🏗️ Architecture Design

### High-Level Flow

```
User Query: "Tell me about your enterprise experience"
     ↓
[1] Generate Query Embedding (Gemini Embedding API)
     gems/embedding-001 → 3072-dim vector
     ↓
[2] Semantic Search in Vector DB (Vertex AI Vector Search)
     cosine similarity search → Top-K results
     ↓
[3] Fetch Relevant JSON Slices from R2
     Only load docs with similarity score >0.75
     ↓
[4] Build Minimal Context (~2KB)
     ↓
[5] Send to Gemini 2.0 Flash for Response
```

### Component Architecture

#### 1. Embeddings Service (`liza-embeddings.js`)
```javascript
// Generate embeddings for user queries
export async function generateQueryEmbedding(query) {
  const model = genAI.getEmbeddingModel('gemini-embedding-001');
  const result = await model.embedContent(query);
  return result.embedding; // 3072-dimensional vector
}

// Batch generate embeddings for all JSON files (one-time setup)
export async function generateDocumentEmbeddings(jsonFiles) {
  const embeddings = [];
  for (const file of jsonFiles) {
    const chunks = chunkDocument(file); // Split large JSONs
    for (const chunk of chunks) {
      const embedding = await generateQueryEmbedding(chunk.text);
      embeddings.push({
        id: `${file.name}_chunk_${chunk.index}`,
        embedding: embedding,
        metadata: {
          source: file.name,
          chunkIndex: chunk.index,
          category: file.category // exp, skills, about, etc.
        }
      });
    }
  }
  return embeddings;
}
```

#### 2. Vector Database Integration (`liza-vector-db.js`)
```javascript
/**
 * Options for Vector DB:
 * A) Vertex AI Vector Search (Recommended - Google ecosystem)
 * B) Pinecone (3rd party, easier setup)
 * C) Chroma (Self-hosted, free)
 */

// Using Vertex AI Vector Search (ScaNN algorithm)
export async function searchVectorDB(queryEmbedding, topK = 5) {
  const endpoint = process.env.VERTEX_AI_VECTOR_SEARCH_ENDPOINT;
  
  const response = await fetch(endpoint, {
    method: 'POST',
    headers: { 'Authorization': `Bearer ${getAccessToken()}` },
    body: JSON.stringify({
      deployed_index_id: 'liza-knowledge-index',
      queries: [{
        datapoint: {
          datapoint_id: 'query',
          feature_vector: queryEmbedding
        },
        neighbor_count: topK
      }]
    })
  });
  
  const results = await response.json();
  return results.nearestNeighbors[0].neighbors.map(n => ({
    id: n.datapoint.datapoint_id,
    score: n.distance,
    metadata: n.datapoint.restricts
  }));
}
```

#### 3. Smart Context Builder v2 (`liza-smart-context-v2.js`)
```javascript
export async function buildSemanticContext(userMessage) {
  console.log(`[Semantic Context] Processing query: "${userMessage}"`);
  
  // 1. Generate embedding for query
  const queryEmbedding = await generateQueryEmbedding(userMessage);
  
  // 2. Search vector DB for relevant chunks
  const results = await searchVectorDB(queryEmbedding, topK = 5);
  
  // 3. Filter results by similarity threshold
  const relevantChunks = results.filter(r => r.score > 0.75);
  
  // 4. Fetch corresponding JSON data from R2
  const sources = [...new Set(relevantChunks.map(r => r.metadata.source))];
  const contextData = await fetchMultipleFromR2(sources);
  
  // 5. Build minimal context with only relevant sections
  const formattedContext = formatSemanticContext(contextData, relevantChunks);
  
  console.log(`[Semantic Context] Generated ${formattedContext.length} bytes from ${sources.length} sources`);
  return formattedContext;
}
```

---

## 🛠️ Implementation Steps

### Pre-requisites
- [x] Google AI Studio Pro membership (USER already has this)
- [ ] Vertex AI project setup
- [ ] Vector Search index deployment
- [ ] Initial embeddings generation for existing JSON files

### Phase 2.1: Setup & Infrastructure (Week 1)

#### Task 1: Enable Vertex AI Vector Search
```bash
# Enable APIs
gcloud services enable aiplatform.googleapis.com
gcloud services enable aiplatform-vectorsearch.googleapis.com

# Create Vector Search index
gcloud ai indexes create \
  --display-name="liza-knowledge-index" \
  --index-file=index-config.json \
  --region=us-central1
```

#### Task 2: Generate Initial Embeddings
```bash
# Run batch embedding generation
node scripts/generate-embeddings.js \
  --source=src/config/*.json \
  --model=gemini-embedding-001 \
  --output=embeddings/liza-knowledge-embeddings.json
```

#### Task 3: Deploy Index
```bash
# Deploy to endpoint
gcloud ai index-endpoints create \
  --display-name="liza-knowledge-endpoint" \
  --network="projects/PROJECT_ID/global/networks/default" \
  --region=us-central1

# Deploy index to endpoint
gcloud ai index-endpoints deploy-index ENDPOINT_ID \
  --deployed-index-id=liza-knowledge-deployed \
  --index=INDEX_ID \
  --display-name="LIZA Knowledge Base"
```

---

### Phase 2.2: Code Integration (Week 2)

#### File Changes

**[NEW]** `src/utils/liza/liza-embeddings.js`
- Generate query embeddings
- Batch process document embeddings
- Handle embedding cache

**[NEW]** `src/utils/liza/liza-vector-db.js`
- Connect to Vertex AI Vector Search
- Perform similarity search
- Handle fallback to keyword search

**[MODIFY]** `api/liza/chat.js`
```diff
- import { buildSmartContext } from '../../src/utils/liza/liza-smart-context.js';
+ import { buildSemanticContext } from '../../src/utils/liza/liza-smart-context-v2.js';
+ import { buildSmartContext } from '../../src/utils/liza/liza-smart-context.js'; // Fallback

  try {
+   // Try semantic search first
+   let contextPrompt;
+   try {
+     const semanticContext = await buildSemanticContext(message);
+     contextPrompt = basePrompt + '\n\n' + semanticContext;
+   } catch (vectorError) {
+     console.warn('[LIZA] Vector DB unavailable, falling back to keyword search');
+     const keywordContext = await buildSmartContext(message);
+     contextPrompt = basePrompt + '\n\n' + keywordContext;
+   }
```

---

### Phase 2.3: Testing & Optimization (Week 3)

#### Test Scenarios

| Query | Expected Behavior semantic search |
|-------|-------|
| "What's your experience in finance?" | Returns BBVA + Scotiabank roles (no "finance" keyword) |
| "Tell me about distributed systems" | Returns roles mentioning Kafka, microservices, K8s |
| "What AI projects have you done?" | Returns LIZA + Richemont/SQLI + MLOps work |
| "Agile transformation experience?" | Returns SAFe + Scrum Master roles across orgs |

#### Performance Benchmarks

| Metric | Keyword (Phase 1) | Semantic (Phase 2) | Target |
|--------|-------------------|---------------------|---------|
| Avg Latency | 800ms | 1200ms | <1500ms |
| Context Size | 2.5KB | 2.0KB | <3KB |
| Accuracy | 70% | 90%+ | >85% |
| False Positives | 20% | <5% | <10% |

---

## 💰 Cost Analysis

### Google AI Studio Pro Inclusions
- ✅ **Gemini Embedding API**: 2M tokens/month free
- ✅ **Vertex AI Vector Search**: First 1M queries free
- ✅ **File Search Tool**: Managed RAG included

### Estimated Monthly Costs (After Free Tier)
- Embeddings: ~5K queries/month × $0.00001/query = **$0.05**
- Vector Search: ~5K queries/month × $0.001/query = **$5.00**
- **Total**: **~$5/month** (assuming moderate traffic)

### ROI Justification
- ✅ Dramatically improves LIZA UX
- ✅ Positions portfolio as cutting-edge (RAG + Vector DB)
- ✅ Scalable to 100+ data sources (blog posts, projects, docs)
- ✅ Minimal cost for production-grade semantic search

---

## 🚧 Risks & Mitigation

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| Vector DB latency >2s | High | Medium | Implement aggressive caching + fallback |
| Embedding quality poor | High | Low | Use gemini-embedding-001 (state-of-the-art) |
| Cost overruns | Medium | Low | Set Cloud billing alerts + rate limiting |
| Complex setup | Medium | Medium | Use File Search Tool (managed RAG) instead |

---

## 🔄 Alternative: File Search Tool (Simpler Approach)

### Option B: Use Gemini's Built-In File Search

Instead of managing Vertex AI Vector Search, leverage Gemini's **File Search Tool**:

```javascript
// Much simpler implementation
const model = genAI.getGenerativeModel({
  model: 'gemini-2.0-flash-exp',
  tools: [{
    fileSearch: {
      // Upload all JSON files to Gemini
      sources: [
        { uri: 'https://assets.hectortechno.com/exp.json' },
        { uri: 'https://assets.hectortechno.com/skills.json' },
        // ... all 7 JSONs
      ]
    }
  }]
});

// Gemini handles embedding + search automatically
const result = await model.generateContent(message);
```

**Pros**:
- ✅ Much simpler setup (no Vertex AI infrastructure)
- ✅ Automatic embeddings + citations
- ✅ Managed RAG system

**Cons**:
- ❌ Less control over search parameters
- ❌ Black-box embedding/retrieval process
- ❌ Requires file uploads (not dynamic R2 fetching)

**Recommendation**: **Start with File Search Tool** for Phase 2.0, migrate to full Vector DB for Phase 2.1 if needed.

---

## 📅 Timeline & Milestones

### Phase 2.0: File Search Tool (2 weeks)
- **Week 1**: Upload JSONs to Gemini, test File Search
- **Week 2**: Integrate, test, deploy

### Phase 2.1: Full Vector DB (4 weeks) - OPTIONAL
- **Week 1**: Vertex AI setup + embedding generation
- **Week 2**: Code integration + fallback logic
- **Week 3**: Testing + optimization
- **Week 4**: Production deployment + monitoring

---

## 🎯 Next Actions (When Ready for Phase 2)

1. **Decision Point**: File Search Tool vs. Full Vector DB?
   - Recommend: Start with File Search (simpler)

2. **Prerequisites**:
   - [ ] Ensure Google AI Pro membership active
   - [ ] Test Gemini File Search API locally
   - [ ] Upload 7 JSON files to Gemini

3. **Implementation**:
   - [ ] Create `liza-file-search.js` wrapper
   - [ ] Modify `api/liza/chat.js` to use File Search
   - [ ] Test with complex queries
   - [ ] Deploy to production

4. **Validation**:
   - [ ] Run semantic query test suite
   - [ ] Measure latency + accuracy improvements
   - [ ] Compare cost vs. Phase 1 baseline

---

**Status**: 📋 DOCUMENTED & READY FOR IMPLEMENTATION  
**Estimated Effort**: 2-4 weeks (depending on File Search vs. Full Vector DB)  
**Priority**: HIGH - Significant LIZA capability enhancement

**Contact Points for Questions**:
- Google AI Studio Pro docs: https://ai.google.dev/
- Vertex AI Vector Search: https://cloud.google.com/vertex-ai/docs/vector-search
- File Search Tool: https://ai.google.dev/gemini-api/docs/file-prompting
