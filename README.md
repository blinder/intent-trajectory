# intent-trajectory

An interactive visualization of intent trajectory encoding, a technique for making retrieval systems aware of reasoning momentum across a query session.

## What this is

Most retrieval systems treat each query independently: embed it, find nearest neighbors, return results. In agentic systems, queries aren't independent. They form a line of reasoning, and each query carries context from the ones before it.

Intent trajectory encoding models that momentum. Rather than retrieving against the current query alone, the system blends the current query embedding with a weighted trajectory vector derived from the session's prior queries. The influence of the trajectory scales with its own *coherence*: the mean cosine similarity between consecutive query pairs. A consistent, focused session earns a stronger trajectory signal. A scattered, exploratory session defaults to standard retrieval behavior.

This visualization shows what that looks like in a five-query company analysis session, with and without trajectory encoding active.

## Live demo

[blinder.github.io/intent-trajectory](https://blinder.github.io/intent-trajectory)

## Related article

[Intent Is Not a Point]([https://dmpengineering.substack.com](https://dmpengineering.substack.com/p/intent-is-not-a-point) · DMP Engineering

## How it works

The visualization is a single self-contained HTML file with no build step. It uses Chart.js for the coherence/alignment line chart and hand-rolled canvas rendering for the scatter plots.

The coordinates are pre-computed 2D PCA-style projections representing a realistic embedding space for a sales intelligence session. The trajectory vector, coherence score, and blended retrieval direction are computed from the same formulas used in the actual `IntentTrajectory` implementation.

## The actual implementation

The `IntentTrajectory` class and its GraphRAG integration are part of [Signal Beam](https://signalbeam.io), a GraphRAG-powered sales intelligence platform. The core module is framework-agnostic and can be plugged into any retrieval system that operates on embeddings.

Key parameters:

- `window_size`: number of past queries to retain (default: 6)
- `half_life_seconds`: recency decay; a query loses half its weight after this many seconds (default: 300)
- `max_beta`: maximum trajectory influence, even at full coherence (default: 0.45)

At `max_beta=0.45`, the current query always contributes at least 55% of the retrieval signal. The trajectory steers, it doesn't override.

## Author

David Palmer · [dmpengineering.substack.com](https://dmpengineering.substack.com)
