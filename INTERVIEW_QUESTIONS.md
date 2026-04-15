# Distributed RAG Interview Questions

This question bank is based on the Distributed RAG project. It contains 500 questions an interviewer may ask about the architecture, implementation, operations, and engineering decisions.

## 1. Project and Architecture

1. What problem does this Distributed RAG project solve?
2. What does RAG mean, and why is it used here?
3. Why is the application divided into multiple services?
4. What are the main services in this project?
5. What is the responsibility of the API gateway?
6. What is the responsibility of the ingestion worker?
7. What is the responsibility of the embedding worker?
8. What is the responsibility of the vector-store service?
9. What is the responsibility of the query service?
10. What role does the frontend play in the architecture?
11. Which components are synchronous and which are asynchronous?
12. Why are ingestion and embedding handled asynchronously?
13. What does the phrase "decoupled data" mean in this project?
14. Which service is the public entry point for client requests?
15. How do services communicate with each other?
16. What are the main external dependencies of the system?
17. What happens from document upload to searchable content?
18. What happens from a user question to a generated answer?
19. Why does the vector-store service own Qdrant access?
20. What benefits do independent service boundaries provide?
21. What are the main disadvantages of this microservice design?
22. Which service would you scale first under heavy upload traffic?
23. Which service would you scale first under heavy query traffic?
24. How would you explain this architecture to a non-technical stakeholder?
25. What is the most important design decision in this project, and why?

## 2. Repository and Configuration

26. What is the purpose of `docker-compose.yml`?
27. What is the purpose of `docker-compose.worker.yml`?
28. Why does each service have its own Dockerfile?
29. What is the purpose of the `requirements.txt` files?
30. Why is the PostgreSQL schema stored under `shared`?
31. What is created by `shared/init.sql`?
32. Why are operational scripts kept in a separate folder?
33. What configuration should be stored in environment variables?
34. Which environment variable is needed for Gemini operations?
35. How does the frontend discover the gateway URL?
36. How are service URLs configured inside Docker Compose?
37. Why do containers use service names such as `redis` and `postgres`?
38. What is the purpose of the Docker Compose network?
39. What is the purpose of named Docker volumes?
40. Which project data survives a container restart?
41. How would you add a new environment variable safely?
42. How would you prevent secrets from being committed to Git?
43. What should be included in an `.env.example` file?
44. How would you configure this project for development without Docker?
45. How would you configure this project for production?
46. What is the purpose of Docker health checks?
47. How does `depends_on` affect service startup?
48. Why are health checks not a complete readiness strategy?
49. How would you inspect the environment of a running service?
50. How would you document a new service added to the repository?

## 3. API Gateway

51. Why should clients communicate through the gateway instead of directly with workers?
52. What endpoints does the gateway expose?
53. How does the gateway handle file uploads?
54. Where are uploaded files stored?
55. How does the gateway protect against unsupported file types?
56. How does the gateway enforce maximum upload size?
57. How does the gateway create a document record?
58. How does the gateway enqueue an ingestion job?
59. How does the gateway proxy a query request?
60. How does the gateway collect system health information?
61. How does the gateway aggregate queue statistics?
62. What is the purpose of the gateway health endpoint?
63. How does CORS affect the gateway?
64. What is risky about allowing all CORS origins?
65. How would you restrict CORS in production?
66. Why does the gateway use an async HTTP client?
67. How does the gateway handle downstream timeouts?
68. How does the gateway handle a failed query service request?
69. How should the gateway validate incoming request data?
70. How would you add authentication to the gateway?
71. How would API keys be stored securely?
72. Why does the schema contain an `api_keys` table?
73. How would you implement per-client rate limiting?
74. What is the purpose of Redis in gateway rate limiting?
75. How would you make gateway request IDs available across services?

## 4. Ingestion Worker

76. How does the ingestion worker receive work?
77. What is the purpose of the `ingestion:queue` Redis queue?
78. How does the worker select the correct extraction method?
79. How are plain text files processed?
80. How are PDF files processed?
81. How are images processed?
82. How are audio files processed?
83. How are videos processed?
84. Why can video ingestion be more expensive than text ingestion?
85. What is OCR, and where is it used?
86. What is Whisper, and where is it used?
87. Why can an image need both OCR and vision captioning?
88. Why are timestamps useful in audio and video chunks?
89. What is the purpose of the configured Whisper model size?
90. Why is local-only Whisper model loading useful?
91. What happens if a Whisper model is unavailable?
92. How does the worker use Ollama for vision processing?
93. Why is Gemini used as an optional vision fallback?
94. What does `VISION_ORDER` control?
95. Why should video frames be sampled instead of processing every frame?
96. What do `VIDEO_VISUAL_MAX_FRAMES` and interval settings control?
97. How does the worker handle temporary file-access failures?
98. Why are ingestion retries necessary in a distributed setup?
99. What happens when an extraction job permanently fails?
100. How does the worker update document lifecycle status?

## 5. Chunking and Document Processing

101. Why must documents be split into chunks?
102. What is the purpose of chunk overlap?
103. What tradeoff exists when increasing chunk size?
104. What tradeoff exists when decreasing chunk size?
105. How would you choose a chunk size for technical documentation?
106. Why should chunks preserve document metadata?
107. What metadata is useful for audio and video chunks?
108. How should page numbers be preserved for PDF chunks?
109. How would you preserve headings during chunking?
110. What problems occur when a sentence is split between chunks?
111. How would you chunk source code differently from prose?
112. How would you chunk tables or structured data?
113. How would you handle a document with no extractable text?
114. How should empty chunks be treated?
115. How do you prevent duplicate chunks during reprocessing?
116. How would you detect duplicate uploaded files?
117. How would you support incremental document updates?
118. How would you preserve the original filename safely?
119. Why should uploaded filenames not be used directly as filesystem paths?
120. How would you handle a corrupt PDF?
121. How would you handle an encrypted PDF?
122. How would you handle an unsupported MIME type?
123. How would you process very large files without exhausting memory?
124. How would you test extraction for every supported media type?
125. What extraction metrics would you monitor?

## 6. Redis Queues and Asynchronous Processing

126. Why is Redis used in this architecture?
127. What is the difference between a queue and a cache?
128. How does the ingestion queue decouple the gateway from processing?
129. How does the embedding queue decouple ingestion from embedding?
130. What happens if a worker is temporarily unavailable?
131. What happens when a queue grows faster than workers can consume it?
132. How would you measure queue lag?
133. How would you detect a stuck queue consumer?
134. What delivery guarantee does the current queue design provide?
135. What is the difference between at-most-once and at-least-once processing?
136. How can a Redis list-based queue lose a job?
137. How would you implement reliable job acknowledgements?
138. How would you implement a dead-letter queue?
139. How would you prevent a poison job from retrying forever?
140. How should retry delays be chosen?
141. What is exponential backoff?
142. Why should retries include jitter?
143. How would multiple workers safely consume the same queue?
144. How would you ensure a job is processed only once logically?
145. What does idempotency mean for ingestion?
146. What does idempotency mean for vector upserts?
147. How would you recover jobs after a worker crash?
148. What Redis data should be persisted?
149. How would Redis memory pressure affect this system?
150. What alternatives to Redis could implement the queues?

## 7. Embedding Worker

151. What is an embedding?
152. Why are embeddings needed for semantic search?
153. What model does the embedding worker use?
154. What is the configured vector dimension?
155. What is the difference between a document embedding and a query embedding?
156. Why should document and query embeddings use compatible task types?
157. How does the worker process an embedding job?
158. Why does the embedding worker call the vector-store service instead of Qdrant directly?
159. What does the worker store in PostgreSQL before embedding?
160. What does the worker store after successful embedding?
161. What happens if the Gemini API returns an error?
162. How should API rate limits be handled?
163. Why does the worker have separate background and query concurrency settings?
164. What risk occurs if query embedding consumes all worker capacity?
165. How would you prioritize interactive query embeddings over batch jobs?
166. How would you batch embedding requests?
167. What are the benefits of embedding batches?
168. What are the risks of embedding batches?
169. How would you support a different embedding provider?
170. What happens if the embedding dimension changes?
171. Why must the Qdrant collection dimension match the embedding dimension?
172. How would you migrate to a new embedding model?
173. How would you compare two embedding models offline?
174. What embedding quality metrics would you track?
175. How would you test that the embedding endpoint works?

## 8. Vector Store and Qdrant

176. Why is Qdrant used in this project?
177. What is a vector database?
178. What is a Qdrant collection?
179. What distance metric does the project use?
180. What does cosine similarity measure?
181. What is stored in a Qdrant point?
182. Why does each vector need a stable point ID?
183. How are point IDs derived in this project?
184. Why is deterministic point identification useful?
185. What metadata should be stored in a vector payload?
186. How does vector search return document context?
187. What is the purpose of the `top_k` parameter?
188. Why should `top_k` have minimum and maximum limits?
189. How does the vector-store service filter results by document?
190. How are vectors deleted when a document is deleted?
191. What happens if a vector exists without its PostgreSQL chunk?
192. What happens if a PostgreSQL chunk exists without its vector?
193. How would you reconcile PostgreSQL and Qdrant state?
194. Why is Qdrant access isolated to one service?
195. What is the purpose of the Redis vector-search cache?
196. When should vector-search cache entries be invalidated?
197. What problems can stale vector-search cache entries cause?
198. How would you scale Qdrant for a larger dataset?
199. What Qdrant index settings might need tuning?
200. How would you test vector-store correctness?

## 9. Query Service and Retrieval

201. What is the responsibility of the query service?
202. How does the query service validate a question?
203. Why should question length be limited?
204. How does the query service obtain a query embedding?
205. How does it retrieve candidate chunks?
206. Why might the service retrieve more candidates than the final top K?
207. What is reranking?
208. Why combine semantic and lexical ranking?
209. What is a recency signal in retrieval?
210. What is reciprocal rank fusion?
211. Why can multi-query retrieval improve recall?
212. What is the risk of generating too many query variations?
213. How should retrieved context be ordered in a prompt?
214. How should context size be limited?
215. What happens when no relevant chunks are found?
216. What does a minimum top-score threshold protect against?
217. What does a minimum average-score threshold protect against?
218. Why is post-answer grounding checked?
219. How can a query service avoid answering from unsupported knowledge?
220. What information should be logged for a query?
221. How should query history be retrieved efficiently?
222. How would you support filters such as document type or date?
223. How would you support metadata-based access control?
224. How would you evaluate retrieval quality?
225. What is the difference between retrieval recall and answer quality?

## 10. LLM Providers and Prompting

226. Which LLM providers does this project support?
227. Why support multiple LLM providers?
228. What is the purpose of the provider list endpoint?
229. How does the system determine whether Groq is available?
230. How does the system determine whether Ollama is available?
231. How does the system determine whether Gemini is available?
232. What are the benefits of hosted inference?
233. What are the benefits of local Ollama inference?
234. What are the disadvantages of local inference?
235. How would you implement provider fallback?
236. When should fallback occur?
237. When should fallback not occur?
238. How would you normalize different provider response formats?
239. How should provider timeouts differ by provider?
240. Why should model names be configurable?
241. How would you validate a requested model name?
242. What is prompt grounding?
243. How should the prompt handle missing context?
244. Why might the prompt enforce a response language?
245. Why should prompts instruct the model not to invent citations?
246. How would you prevent prompt injection from uploaded documents?
247. How would you separate system instructions from retrieved content?
248. How would you limit generated answer length?
249. How would you stream an answer to the frontend?
250. How would you evaluate generated answer faithfulness?

## 11. PostgreSQL and Data Model

251. What is stored in the `documents` table?
252. What is stored in the `chunks` table?
253. What is stored in the `embedding_jobs` table?
254. What is stored in the `query_logs` table?
255. Why does the schema use UUIDs?
256. Why does `chunks.document_id` reference `documents`?
257. What does `ON DELETE CASCADE` do here?
258. Why is chunk metadata stored as JSONB?
259. What values can a document status contain?
260. What values can an embedding job status contain?
261. How does the document lifecycle work?
262. How should failed documents expose their error message?
263. Why are timestamps stored with time zones?
264. Which indexes are defined in the schema?
265. Why is `documents.status` indexed?
266. Why is `chunks.document_id` indexed?
267. Why is `embedding_jobs.status` indexed?
268. How would you paginate the document list endpoint?
269. How would you paginate query history?
270. How would you prevent unbounded query-log growth?
271. What transaction boundaries should ingestion use?
272. How can a transaction improve document consistency?
273. What happens if the process crashes during a transaction?
274. How would you migrate the schema safely?
275. How would you back up this database?

## 12. Frontend and Next.js

276. Why is Next.js used for the frontend?
277. What is the role of the App Router?
278. What does `app/page.tsx` contain?
279. What does `app/layout.tsx` contain?
280. What belongs in `globals.css`?
281. Why are UI features split into components?
282. What is the role of `lib/api.ts`?
283. How does the frontend upload a file?
284. How does the frontend display document progress?
285. How does the frontend submit a question?
286. How does the frontend display Markdown answers?
287. How does the frontend select an LLM provider?
288. How does the frontend manage Ollama models?
289. How does the frontend display system health?
290. How should the frontend handle gateway downtime?
291. How should the frontend display a failed document?
292. How would you prevent duplicate form submissions?
293. How would you show upload progress for a large file?
294. How would you cancel an in-progress request?
295. How would you handle a slow query response?
296. How would you make the UI accessible?
297. How would you test the frontend components?
298. How would you test the complete upload workflow?
299. How would you protect frontend environment variables?
300. What frontend state should be local and what should be server state?

## 13. Docker and Deployment

301. Why containerize each backend service?
302. What should a service Dockerfile contain?
303. Why should containers run with a minimal base image?
304. How would you reduce Docker image size?
305. Why might the ingestion image be larger than the gateway image?
306. How are uploads shared between services?
307. Why must remote workers access the same upload files?
308. What does `SHARED_UPLOADS_DIR` control?
309. How would you run an additional ingestion worker?
310. How would you run an additional embedding worker?
311. What is horizontal scaling in this project?
312. What is vertical scaling in this project?
313. Which services can be scaled independently?
314. What are the risks of scaling workers without limits?
315. How would you deploy this architecture to Kubernetes?
316. What Kubernetes object would represent each service?
317. How would you provide secrets in Kubernetes?
318. How would you configure readiness probes?
319. How would you configure liveness probes?
320. How would you expose the gateway publicly?
321. How would you secure internal service traffic?
322. How would you implement rolling deployments?
323. How would you handle database migrations during deployment?
324. How would you monitor container resource usage?
325. How would you roll back a bad deployment?

## 14. Reliability and Failure Handling

326. What happens if Redis goes down?
327. What happens if PostgreSQL goes down?
328. What happens if Qdrant goes down?
329. What happens if Gemini is unavailable?
330. What happens if Groq is unavailable?
331. What happens if Ollama is unavailable?
332. What happens if the gateway restarts during an upload?
333. What happens if ingestion crashes after saving chunks?
334. What happens if embedding crashes after vector upsert?
335. How can partial processing be detected?
336. How can a job be retried safely?
337. What is the difference between a transient and permanent failure?
338. Which errors should be retried?
339. Which errors should fail immediately?
340. How would you add circuit breakers?
341. Why are timeouts required for every network call?
342. How do retry counts affect user-visible latency?
343. How would you avoid retry storms?
344. How would you handle backpressure?
345. How would you keep a worker from taking too many jobs at once?
346. How would you recover after a machine failure?
347. What data should be backed up together for recovery?
348. How would you verify a restored system?
349. What is graceful shutdown for a worker?
350. How would you test failure scenarios automatically?

## 15. Performance and Scaling

351. What are the likely bottlenecks in this system?
352. How would you measure end-to-end upload latency?
353. How would you measure end-to-end query latency?
354. Which query stages should be timed separately?
355. How would you improve PDF extraction throughput?
356. How would you improve embedding throughput?
357. How would you improve vector-search latency?
358. How would you improve LLM response latency?
359. How does connection pooling improve performance?
360. What does `DB_POOL_MIN` control?
361. What does `DB_POOL_MAX` control?
362. How can too many database connections hurt PostgreSQL?
363. How would you tune worker concurrency?
364. How does concurrency affect API rate limits?
365. How would you batch small embedding jobs?
366. How would you cache repeated queries?
367. How would you invalidate query caches after document updates?
368. How would you reduce prompt token usage?
369. How would you reduce unnecessary retrieved chunks?
370. How would you optimize large document uploads?
371. How would you handle millions of vectors?
372. How would you partition or shard data?
373. How would you distribute workers across machines?
374. What performance metrics belong on the system dashboard?
375. How would you create a performance benchmark for this project?

## 16. Security

376. What security risks exist in the upload endpoint?
377. Why is MIME type validation alone insufficient?
378. How would you validate file contents securely?
379. How would you prevent path traversal?
380. How would you prevent malicious filenames?
381. How would you scan uploaded files for malware?
382. How would you limit upload size at multiple layers?
383. How would you authenticate API clients?
384. How would you authorize access to documents?
385. How would you isolate users' documents?
386. How would you prevent cross-tenant retrieval?
387. How should API keys be hashed?
388. Why should raw API keys not be stored in PostgreSQL?
389. How should Gemini and Groq keys be protected?
390. Why should secrets not be exposed to the frontend?
391. What is the risk of permissive CORS?
392. How would you protect internal service endpoints?
393. How would you protect Redis from unauthorized access?
394. How would you protect PostgreSQL from unauthorized access?
395. How would you protect Qdrant?
396. What sensitive information could query logs contain?
397. How would you redact sensitive content from logs?
398. How would you prevent prompt injection?
399. How would you prevent denial-of-service through expensive media processing?
400. What security tests would you run before deployment?

## 17. Observability and Operations

401. What logs does each service need?
402. Why should logs include a service name?
403. Why are request IDs useful?
404. How would you trace one upload across all services?
405. How would you trace one query across all services?
406. What health checks should infrastructure services expose?
407. What is the difference between health and readiness?
408. What metrics should the gateway expose?
409. What metrics should the ingestion worker expose?
410. What metrics should the embedding worker expose?
411. What metrics should the vector store expose?
412. What metrics should the query service expose?
413. How would you measure queue depth?
414. How would you measure job success rate?
415. How would you measure retry rate?
416. How would you measure provider error rate?
417. How would you measure retrieval quality in production?
418. How would you alert on a growing queue?
419. How would you alert on repeated failures?
420. How would you inspect Docker Compose logs?
421. How would you inspect a failed document's status?
422. How would you investigate missing search results?
423. How would you investigate slow queries?
424. How would you investigate a worker that appears healthy but does no work?
425. What operational runbook should this project have?

## 18. Testing

426. What unit tests should be written for the gateway?
427. What unit tests should be written for the ingestion worker?
428. What unit tests should be written for the embedding worker?
429. What unit tests should be written for the vector-store service?
430. What unit tests should be written for the query service?
431. How would you mock Gemini in tests?
432. How would you mock Groq in tests?
433. How would you mock Ollama in tests?
434. How would you mock Redis queues?
435. How would you mock PostgreSQL?
436. How would you mock Qdrant?
437. What is an integration test for this project?
438. What is an end-to-end test for this project?
439. How would you test the upload-to-search workflow?
440. How would you test the query workflow?
441. How would you test retry behavior?
442. How would you test idempotency?
443. How would you test malformed files?
444. How would you test very large files?
445. How would you test concurrent uploads?
446. How would you test concurrent queries?
447. How would you test provider fallback?
448. How would you test a failed vector upsert?
449. How would you test stale cache invalidation?
450. What should run in CI for every pull request?

## 19. Design Decisions and Improvements

451. Why use FastAPI instead of a synchronous Python web framework?
452. Why use async database and HTTP clients?
453. What parts of the code are still CPU-bound despite async code?
454. When should CPU-bound work use a process pool?
455. Would a task broker such as Celery improve this design?
456. Would Kafka be appropriate for these queues?
457. Would a monolith be simpler for this project?
458. When would you choose a monolith over these microservices?
459. What service boundaries might be changed in a redesign?
460. Should ingestion and embedding share a database schema?
461. Should the gateway access PostgreSQL directly?
462. Should the query service access Qdrant directly?
463. How would you add document versioning?
464. How would you add user accounts?
465. How would you add multi-tenancy?
466. How would you add document expiration?
467. How would you add support for web pages as first-class documents?
468. How would you add citations to answers?
469. How would you add hybrid keyword and vector search?
470. How would you add a reranking model?
471. How would you add OCR language selection?
472. How would you add more embedding providers?
473. How would you add an evaluation dataset?
474. How would you add human feedback on answers?
475. What is the first improvement you would make to this project?

## 20. Practical Interview Scenarios

476. A user uploads a PDF, but its status remains `queued`. How do you debug it?
477. A document reaches `done`, but search returns no results. What do you inspect?
478. Search returns unrelated chunks. Which retrieval settings do you investigate?
479. Answers contain information not present in the documents. How do you diagnose it?
480. The embedding queue grows continuously. What actions do you take?
481. Gemini starts returning rate-limit errors. How do you protect the system?
482. Qdrant reports a vector-dimension error. What caused it?
483. PostgreSQL is full. What data and configuration do you inspect?
484. Redis memory usage is increasing. What could be causing it?
485. The frontend shows a CORS error. How do you debug it?
486. The gateway is healthy but queries fail. Which services do you check?
487. The query service is healthy but answers are slow. How do you isolate the delay?
488. Ollama models do not appear in the frontend. What do you check?
489. A remote worker cannot process uploaded files. What configuration is likely wrong?
490. A worker crashes repeatedly on one video. How do you contain the problem?
491. Duplicate vectors appear after a retry. How would you fix the design?
492. A user deletes a document, but its content is still retrieved. What could be wrong?
493. A deployment changes the embedding model. How do you migrate existing vectors?
494. The system works locally but fails in Docker. What categories of differences do you inspect?
495. The system works in Docker but fails on another machine. What environment issues do you inspect?
496. How would you demonstrate this project in a technical interview?
497. What part of the project would you explain with a diagram?
498. What tradeoff in this project would you defend most strongly?
499. What weakness in this project would you acknowledge to an interviewer?
500. If given one week to improve this project, what would you implement first and why?
