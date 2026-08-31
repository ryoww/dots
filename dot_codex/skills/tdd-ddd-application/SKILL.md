---
name: tdd-ddd-application
description: "Build or extend applications with test-driven development, domain-driven design, and a core-first architecture. Use for new applications, business features, use cases, domain-model changes, or UI/CLI/API work that must keep business rules in entities and use cases and complete the testable application core before adding delivery adapters."
---

# TDD・DDD Application Development

Build the application core first. Complete domain behavior and application use cases through TDD before implementing UI, CLI, API, database, or other external adapters.

## Preserve the core boundary

Treat the following as the application core:

- entities, value objects, aggregates, domain services, and domain events;
- application services and use cases;
- input/output data structures owned by the use cases;
- ports owned by the core for persistence, clocks, identifiers, messaging, and external services.

Keep the core independent of UI frameworks, CLI parsers, web frameworks, databases, networks, filesystems, environment variables, standard streams, and process-exit behavior. Let outer adapters depend inward:

```text
UI / CLI / API / infrastructure -> application use cases -> domain model
```

Define ports in the core and implement them in outer layers. Do not place business rules in controllers, commands, views, ORM models, or framework callbacks.

Project scaffolding, build configuration, and the test runner may be prepared early. Do not implement feature behavior in an adapter as a temporary shortcut.

## Calibrate DDD to the problem

Use domain language in requirements, tests, and code. Identify business invariants and transaction boundaries before choosing tactical patterns.

Introduce entities, value objects, aggregates, repositories, domain services, and events only when their semantics are present. For CRUD-like or technically simple behavior, keep the model small instead of manufacturing layers and abstractions. Preserve an existing project's terminology and architecture when they already express the same boundaries.

## Follow the workflow

### 1. Discover behavior

Inspect the existing code, tests, and project conventions. Express the requested behavior as use cases with examples, acceptance criteria, domain terms, invariants, and failure cases. State only assumptions that affect observable behavior.

For existing behavior without adequate tests, add characterization tests before changing it.

### 2. Plan the core

Identify the smallest domain model and use-case boundary that can satisfy one behavior slice. Decide which external capabilities require ports. Keep framework and storage choices outside this plan unless they constrain an explicit requirement.

### 3. Build the domain with TDD

Repeat for each domain behavior:

1. Write one failing test that describes observable behavior.
2. Run it and confirm that it fails for the intended reason.
3. Write the minimum domain code needed to pass.
4. Run the relevant tests.
5. Refactor names, duplication, invariants, and boundaries while tests remain green.

Prefer tests of behavior and invariants over tests of private methods or implementation structure.

### 4. Build use cases with TDD

Test use cases through their public input and output boundary. Supply in-memory adapters or deterministic fakes for ports so tests remain inside program memory. Cover the successful path, business-rule failures, and meaningful boundary cases.

Repeat Red, Green, and Refactor for each use case. Do not begin a real UI, CLI, API, database, or network adapter merely to make the use case testable.

### 5. Enforce the core gate

Do not implement delivery or infrastructure adapters until all applicable checks pass:

- every requested behavior is reachable through a use-case API without UI or CLI;
- domain invariants are enforced in the domain model;
- use-case tests run with in-memory adapters or deterministic fakes;
- the domain and application layers import no delivery or infrastructure framework;
- relevant core tests pass;
- remaining adapter requirements are data translation, orchestration, and integration concerns rather than missing business behavior.

Count a test as passing only when it has run successfully in the target project environment. Planned tests, code excerpts, mental execution, or a test that cannot run do not satisfy the gate. If the gate fails or cannot be verified, stop before writing or proposing production adapter code; continue working in the core and report the blocker. You may describe the future adapter contract, but do not implement it. Explain any requirement that genuinely cannot be modeled or tested without an external boundary before making an exception.

### 6. Add thin adapters

After the core gate passes, implement the requested UI, CLI, API, persistence, and external-service adapters. Limit each adapter to:

1. parse or receive external input;
2. translate it into a use-case input;
3. invoke the use case;
4. translate the result into external output;
5. map technical failures at the boundary.

Keep business decisions in the core. Add focused adapter tests and integration tests; do not duplicate core business scenarios at every layer.

### 7. Verify the whole change

Run focused tests during each TDD cycle, then run the project's relevant full test, static-analysis, and formatting commands. Never report a test as passing unless it was executed. Distinguish failures caused by the change from pre-existing or environmental failures.

## Report completion

Summarize:

- use cases, domain terms, and invariants implemented;
- the Red/Green evidence or tests added before production code;
- the core-gate result;
- adapters added after the gate;
- commands run and their results;
- assumptions, deferred cases, and remaining risks.
