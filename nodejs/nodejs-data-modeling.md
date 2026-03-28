# Data Modeling For Node.js Applications

This file is the conceptual data-modeling guide for the Node.js folder. Read it before the ORM- or ODM-specific notes.

## TL;DR

Good data modeling starts with access patterns, relationships, consistency needs, and scaling tradeoffs, not with the library you happen to install first.

## SQL Vs NoSQL

### SQL

Best when you want:

- Strong relational structure
- Clear joins and associations
- Transaction-heavy workflows
- Strict schema control

### NoSQL

Best when you want:

- Flexible document structure
- Rapid iteration on evolving data shape
- Document-oriented access patterns
- Simpler read paths for denormalized data

### Interview Answer

I choose SQL when relationships, transactions, and consistency rules dominate. I choose NoSQL when document access patterns and flexible schema evolution matter more.

## Relationship Types

### One-To-One

One record maps to one other record.

### One-To-Many

One parent has multiple children.

### Many-To-Many

Many records relate to many others, usually through a join or linking structure.

## Embedding Vs Referencing

### Embed When

- Related data is read together often
- The child data is tightly coupled to the parent
- Document growth stays manageable

### Reference When

- Child data is reused elsewhere
- Relationships are large or many-to-many
- Independent updates matter

### Interview Answer

Embedding improves read simplicity when data belongs together. Referencing is better when relationships are large, reused, or updated independently.

## Validation And Constraints

### What To Know

- Database constraints protect integrity
- Application validation protects API boundaries
- You usually want both, not only one

## Indexing

### Why It Matters

Indexes improve query speed but add write cost and storage overhead.

### Common Trap

Adding too many indexes without understanding query patterns.

## Rapid-Fire Questions

### SQL vs NoSQL?

Choose based on relationship complexity, consistency needs, and access patterns.

### Embed vs reference?

Embed for tightly coupled read-together data. Reference for reusable or independently changing data.
