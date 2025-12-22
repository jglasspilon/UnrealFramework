# Case Study: Data-Driven AR&VR Graphic Framework for Broadcast

A scalable, event-driven Unreal Engine framework for managing dynamic broadcast graphics, real-time data ingestion, and runtime UI binding.
Designed to support multiple data sources, rapid content switching, and designer-friendly extensibility while keeping systems decoupled, testable, and maintainable.

---

## Design Philosophy

This framework was designed around **separation of concerns, scalability, and non-invasive extensibility**.  
The goal was to allow teams to introduce new content types, data sources, and UI bindings without rewriting existing systems or introducing brittle dependencies.

Key principles:
- Event-driven, loosely coupled architecture  
- Real-time, data-driven configuration via JSON  
- Clear ownership of responsibility across layers  
- Designer and integrator-friendly extension points  

The systems below demonstrate how these principles were applied across content management, data ingestion, and UI binding. Expand a summary to explore the design challenge, technical approach, and resulting impact.

---

## Relevant Tech Stack
![Unreal Engine](https://img.shields.io/badge/Unreal-0E1128?style=for-the-badge&logo=unrealengine&logoColor=white)
![Blueprints](https://img.shields.io/badge/Blueprints-1A6DFF?style=for-the-badge)
![JSON](https://img.shields.io/badge/JSON-000000?style=for-the-badge&logo=json&logoColor=white)
![HTTP](https://img.shields.io/badge/HTTP-005571?style=for-the-badge)

---

## Framework Breakdown
<details>
  <summary><strong>"Content Management Framework"</strong><br>
  A modular system for routing, activating, and updating runtime content across multiple zones using a centralized manager for singular external hooking, eliminating code duplication, and vastly improving code maintainability.</summary>

  <blockquote>

  <h3>Outcome</h3>
  <p>
    Enabled dynamic content switching and updates across multiple runtime zones using a single orchestration layer, reducing coupling between content logic and presentation.
  </p>

  <h3>Responsibilities</h3>
  <ul>
    <li>Designed a centralized Content Manager responsible for routing content updates via JSON payloads</li>
    <li>Defined Content Zones as independently managed runtime areas</li>
    <li>Created an abstract Content base class with a predictable lifecycle (Prepare, Update, Cleanup)</li>
    <li>Implemented enum-based content and zone identification for safety and clarity</li>
  </ul>

  <h3>Challenge</h3>
  <p>
    Managing multiple dynamic content areas often leads to tightly coupled logic, duplicated update paths, and brittle state management. As content types grow, systems become harder to reason about and modify safely.
  </p>

  <h3>Solution</h3>
  <p>
    Introduce a Content Manager that routes JSON-driven commands to individual Content Zones. Each zone owns its active content state and manages transitions internally, while content implementations remain unaware of higher-level orchestration.
  </p>

  <h3>Impact</h3>
  <ul>
    <li><strong>Before ➙</strong> content logic tightly coupled to specific UI or world actors, leading to duplication, maintainability issues and errors across transitions.</li>
    <li><strong>After ➙</strong> clean separation between content routing, state management, and rendering, eliminating code duplication, and vastly improving code maintainability.</li>
  </ul>

  <h3>Key Learnings</h3>
  <ul>
    <li>Explicit ownership of states and their transitions dramatically simplifies content lifecycle management</li>
    <li>Abstracting out transition logic from content allows for faster graphic iteration without worry of lifecycle management</li>
  </ul>

  </blockquote>
</details>

<details>
  <summary><strong>"Connection & Data Ingestion Framework"</strong><br>
  A pluggable data ingestion layer supporting protocol agnostic communication and unified data access, abstracting out any protocol specific logic while providing a single source of truth for other systems to hook into.</summary>

  <blockquote>

  <h3>Outcome</h3>
  <p>
    Enabled the framework to ingest, manage, and distribute data from multiple external sources without hard dependencies between systems.
  </p>

  <h3>Responsibilities</h3>
  <ul>
    <li>Designed a Connection Manager to orchestrate data source lifecycles</li>
    <li>Implemented connection settings as extensible, type-driven objects</li>
    <li>Defined a common connection interface to support HTTP, MQTT, file readers, and future sources</li>
    <li>Centralized parsed data storage within an in-memory Data Center</li>
  </ul>

  <h3>Challenge</h3>
  <p>
    Supporting multiple data sources often leads to fragmented logic, duplicated parsing code, and rigid pipelines that are difficult to extend or test.
  </p>

  <h3>Solution</h3>
  <p>
    Abstract all data ingestion behind a common connection interface and route parsed data into a centralized Data Center. This allows content and UI systems to remain agnostic of how data is retrieved.
  </p>

  <h3>Impact</h3>
  <ul>
    <li><strong>Before ➙</strong> data source changes required modifications across multiple systems</li>
    <li><strong>After ➙</strong> new data sources added with minimal or zero impact on consumers</li>
  </ul>

  <h3>Key Learnings</h3>
  <ul>
    <li>Decoupling data acquisition from data consumption improves system longevity</li>
    <li>Factory-driven creation simplifies onboarding new connection types</li>
  </ul>

  </blockquote>
</details>

<details>
  <summary><strong>"Event-Driven Data Binding Layer"</strong><br>
  A subscription-based data binding system that keeps runtime content and UI synchronized all while maintaining a loose component based integration workflow to provide design and integration flexibility.</summary>

  <blockquote>
  <h3>Outcome</h3>
  <p>
    Allowed content and UI elements to react automatically to data changes without polling or tight coupling to data sources. The component based approach also allowed for fast iteration when creating or modyfing graphics.
  </p>

  <h3>Responsibilities</h3>
  <ul>
    <li>Designed a Data Bindable interface for subscription-based updates</li>
    <li>Implemented an intuitive JSON parsable binding structure mapping to stored JSON data</li>
    <li>Integrated auto-update and manual update control for performance tuning</li>
  </ul>

  <h3>Challenge</h3>
  <p>
    Keeping UI and content synchronized with frequently changing data often leads to inefficient polling or tightly coupled update logic. Binding data to UI also generally requires manual mapping of data which increases the code base as graphics and data grow.  
  </p>

  <h3>Solution</h3>
  <p>
    Introduce an event-driven subscription model where binders register interest in specific data streams. The Data Center pushes updates only when relevant data changes. These data binders would be developed as components giving designers freedom to iterate on their designs with easy plug-and-play access to runtime data.
  </p>

  <h3>Impact</h3>
  <ul>
    <li><strong>Before ➙</strong> frequent polling and duplicated update logic leading to bloated code base</li>
    <li><strong>After ➙</strong> efficient, reactive updates on plug-and-play components leading to observably improved performance, improved code maintainability by reducing duplication and bload, faster iteration speed for integration.</li>
  </ul>

  <h3>Key Learnings</h3>
  <ul>
    <li>Event-driven architectures scale better under frequent updates</li>
    <li>Explicit subscriptions make data flow easier to reason about and debug</li>
    <Li>Component based architecture gives more creative freedom upon integration increasing iteration speed</Li>
  </ul>

  </blockquote>
</details>

---

## Framework Overview
*Diagrams shown are conceptual and represent high-level architectural patterns.
Names and structures have been generalized to avoid disclosure of proprietary implementations.*

### Visual Architecture Overview
> High-level view of the framework showing how content management, data ingestion, and event-driven data binding interact through decoupled systems. Emphasizes separation of concerns and scalable data flow.
> ![Framework Overview](Media/FrameworkOverview.png)

### Content Management Pipeline
> Illustrates how JSON-driven commands are routed through a centralized manager to activate, update, and transition content across independent runtime zones. Content lifecycles are handled locally to avoid global state coupling.
> 
> ![Content Management Pipeline](Media/ContentManagementPipeline.png)

### Data Ingestion & Distribution Pipeline
> Shows a protocol-agnostic data ingestion layer feeding parsed data into a centralized Data Center that broadcasts updates to subscribed systems. Enables multiple data sources without hard dependencies between consumers.
> 
> ![Data Management Pipeline](Media/DataManagementPipeline.png)
