# Request for Comments — Opt-In

**Extension**: Request for Comments (RFC)

## Opt-In Prompt

The following question is automatically included in the Requirements Analysis clarifying questions when this extension is loaded:

```markdown
## Question: Request for Comments Extension
Should a Request for Comments (RFC) stage be executed for this project to deliberate asynchronously on major product and architectural decisions before design and construction?

The RFC stage is most valuable when **both** are true:
- The decision is **major** (high-impact, hard to reverse, cross-cutting, or affecting public APIs / compliance posture / core technology)
- The team is **distributed or virtual** and synchronous communication is difficult (stakeholders span time zones, work asynchronously, or cannot reliably gather in real time)

A) Yes — execute the RFC stage as a blocking gate for all qualifying decisions (recommended for distributed/async teams making major architectural, cross-system, public-API, migration, or compliance decisions)
B) On-demand — load RFC rules but only execute the stage when I explicitly request it for a specific decision (suitable for hybrid teams or teams that already have lightweight informal alignment for most work)
C) No — skip the RFC stage entirely (suitable for co-located teams that deliberate synchronously, prototypes, PoCs, single-developer projects, or work with no architectural decisions involved)
X) Other (please describe after [Answer]: tag below)

[Answer]: 
```
