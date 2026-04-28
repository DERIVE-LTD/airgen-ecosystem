# Embedding `sysml-reactflow` in your own application

`sysml-reactflow` is the open-source React component library that
powers Reify's diagrams. It implements the OMG SysML v2.0
specification with 60+ node types, 30+ edge types, and five layout
algorithms via `elkjs`. You can embed it in any React application —
docs sites, dashboards, internal tooling, or your own MBSE product.

This guide walks through installation, the minimum viable example,
the SysML v2 viewpoint system, and the layout API.

> **Prerequisites:** a React 18+ application using Vite, Next.js, or
> CRA. Some familiarity with React and a passing acquaintance with
> SysML v2 concepts.

## Install

```sh
npm install sysml-reactflow react react-dom @xyflow/react
```

`sysml-reactflow` targets React Flow v12 (`@xyflow/react`). The
legacy `reactflow` v11 package is **not** supported.

## Minimum viable example

```tsx
import { ReactFlowProvider } from "@xyflow/react";
import {
  SysMLDiagram,
  createNodesFromSpecs,
  createEdgesFromRelationships,
} from "sysml-reactflow";

const nodes = createNodesFromSpecs([
  {
    kind: "requirement-usage",
    spec: {
      id: "REQ-101",
      name: "Thermal Compliance",
      text: "The payload shall remain between 0 °C and 40 °C.",
      status: "reviewed",
    },
  },
  {
    kind: "part-definition",
    spec: {
      id: "PART-42",
      name: "ThermalController",
      description: "Regulates loop temperature.",
      attributes: [{ name: "pump", type: "Pump", multiplicity: "[2]" }],
      ports: [
        { name: "coolantIn", type: "Coolant", direction: "in" },
        { name: "coolantOut", type: "Coolant", direction: "out" },
      ],
    },
  },
]);

const edges = createEdgesFromRelationships([
  {
    id: "edge-1",
    type: "satisfy",
    source: "PART-42",
    target: "REQ-101",
    label: "satisfy",
  },
]);

export function ThermalDemo() {
  return (
    <ReactFlowProvider>
      <div style={{ width: "100%", height: 520 }}>
        <SysMLDiagram nodes={nodes} edges={edges} />
      </div>
    </ReactFlowProvider>
  );
}
```

That's a fully styled SysML v2 diagram in twenty lines. Drop this
into a Vite or Next playground and you'll see the requirement, the
part, and a `satisfy` edge between them.

## Element coverage

The library exports 60+ factory functions covering every major SysML
v2 metaclass:

### Structural (16 types)

`createPartDefinitionNode`, `createPartUsageNode`,
`createAttributeDefinitionNode`, `createAttributeUsageNode`,
`createPortDefinitionNode`, `createPortUsageNode`,
`createItemDefinitionNode`, `createItemUsageNode`,
`createConnectionDefinitionNode`, `createConnectionUsageNode`,
`createInterfaceDefinitionNode`, `createInterfaceUsageNode`,
`createAllocationDefinitionNode`, `createAllocationUsageNode`,
`createReferenceUsageNode`, `createOccurrenceDefinitionNode`,
`createOccurrenceUsageNode`.

### Behavioural (14 types)

`createActionDefinitionNode`, `createActionUsageNode`,
`createActivityNode`, `createCalculationDefinitionNode`,
`createCalculationUsageNode`, `createPerformActionNode`,
`createSendActionNode`, `createAcceptActionNode`,
`createAssignmentActionNode`, `createIfActionNode`,
`createForLoopActionNode`, `createWhileLoopActionNode`,
`createStateDefinitionNode`, `createStateUsageNode`,
`createTransitionUsageNode`, `createExhibitStateNode`.

### Requirements & cases (12 types)

`createRequirementDefinitionNode`, `createRequirementUsageNode`,
`createConstraintDefinitionNode`, `createConstraintUsageNode`,
`createVerificationCaseDefinitionNode`,
`createVerificationCaseUsageNode`,
`createAnalysisCaseDefinitionNode`, `createAnalysisCaseUsageNode`,
`createUseCaseDefinitionNode`, `createUseCaseUsageNode`,
`createConcernDefinitionNode`, `createConcernUsageNode`.

### Organisational & metadata (8 types)

`createPackageNode`, `createLibraryPackageNode`,
`createInteractionNode`, `createSequenceLifelineNode`,
`createMetadataDefinitionNode`, `createMetadataUsageNode`,
`createCommentNode`, `createDocumentationNode`.

### Edges (30+ types)

Type relationships (Specialization, Conjugation, Feature Typing,
Subsetting, Redefinition), dependencies (Satisfy, Verify, Refine,
Allocate), flows (Control Flow, Item Flow, Action Flow, Flow
Connection), structural (Feature Membership, Owning Membership,
Variant Membership), connectors (Binding Connector, Succession), use
case (Include, Extend), state (Transition), interaction (Message,
Succession).

## Viewpoints

SysML v2 makes diagrams *view specifications*. The library ships
ready-made viewpoints that filter the model to the right nodes and
edges for each view type:

```tsx
import {
  SysMLDiagram,
  structuralDefinitionViewpoint,
  type SysMLModel,
} from "sysml-reactflow";

const model: SysMLModel = { nodes: specs, relationships };

<SysMLDiagram model={model} viewpoint={structuralDefinitionViewpoint} />;
```

Eight viewpoints ship out of the box: structural-definition,
structural-usage, behavioural, state, sequence, requirement,
analysis, and use-case. Use them as-is, compose them, or write
your own.

## Automatic layout

`elkjs` is bundled. Pick the algorithm that fits your diagram type:

| Algorithm        | Best for                                                  |
| ---------------- | --------------------------------------------------------- |
| **Layered**      | BDD, requirements, packages, activities (hierarchical).   |
| **Force**        | State machines, use cases, dense graphs.                  |
| **Tree**         | Package hierarchies.                                      |
| **Box / orth.** | IBD, component compositions.                              |
| **Sequence**     | Sequence diagram lifelines.                               |

```tsx
import { layoutAndRouteFromSpecs } from "sysml-reactflow";

const { nodes: layoutedNodes, edges: layoutedEdges } =
  await layoutAndRouteFromSpecs(specs, relationships, "requirements", {
    measure: true,
  });

<SysMLDiagram nodes={layoutedNodes} edges={layoutedEdges} fitView />;
```

The `measure: true` option sizes nodes from their actual content
before layout — slower, but produces tighter results.

For deep customisation, see the package's [`LAYOUT.md`](https://github.com/Hollando78/SysML-reactflow/blob/main/LAYOUT.md).

## Theming

The library uses CSS variables, so theming integrates cleanly with
your app's design system:

```css
:root {
  --sysml-node-bg: #ffffff;
  --sysml-node-text: #111827;
  --sysml-node-border: #e5e7eb;
  --sysml-edge-color: #6b7280;
  /* ... */
}

:root.dark {
  --sysml-node-bg: #1f2937;
  --sysml-node-text: #f3f4f6;
  /* ... */
}
```

A full list of variables lives in the package's documentation.
Reify uses an IBM Carbon-derived categorical palette by default.

## A complete component

Here's a self-contained component that fetches a Reify diagram via
the v1 HTTP API and renders it:

```tsx
import { useEffect, useState } from "react";
import { ReactFlowProvider } from "@xyflow/react";
import { SysMLDiagram, layoutAndRouteFromSpecs } from "sysml-reactflow";

export function ReifyDiagram({
  reifyUrl,
  token,
  slug,
  type = "bdd",
  scope,
}: {
  reifyUrl: string;
  token: string;
  slug: string;
  type?: string;
  scope?: string;
}) {
  const [graph, setGraph] = useState<{ nodes: any[]; edges: any[] } | null>(
    null
  );

  useEffect(() => {
    const url =
      `${reifyUrl}/api/v1/projects/${slug}/diagrams/${type}` +
      (scope ? `?scope=${encodeURIComponent(scope)}` : "");

    fetch(url, { headers: { Authorization: `Bearer ${token}` } })
      .then((r) => r.json())
      .then(async (raw) => {
        // Reify returns specs + relationships; layout client-side
        const laid = await layoutAndRouteFromSpecs(
          raw.specs,
          raw.relationships,
          type,
          { measure: true }
        );
        setGraph(laid);
      });
  }, [reifyUrl, token, slug, type, scope]);

  if (!graph) return <div>Loading…</div>;

  return (
    <ReactFlowProvider>
      <div style={{ width: "100%", height: 600 }}>
        <SysMLDiagram nodes={graph.nodes} edges={graph.edges} fitView />
      </div>
    </ReactFlowProvider>
  );
}
```

Drop that into your app and you have an embedded Reify view that
respects your token's permissions and your app's theming.

## Storybook and live demos

The library ships a Storybook with one story per node type and edge
type, and per-diagram-type integration stories. The hosted build is
at [`hollando78.github.io/SysML-reactflow`](https://hollando78.github.io/SysML-reactflow/) —
useful for confirming a node type or edge before you wire it.

## Limitations

The library is currently:

- **Visualisation-focused.** No XMI / JSON SysML v2 interchange
  serialisation.
- **Read-rendering biased.** Interactive editing is not built in
  (Reify's editor is a CodeMirror layer over the SysML text, not an
  in-canvas WYSIWYG).
- **Simplified expressions.** Expression metaclasses (literal,
  invocation, feature reference) are represented as strings.

For full SysML v2 authoring, you'd combine `sysml-reactflow` for
rendering with the
[Eclipse SysML v2 API](https://github.com/Systems-Modeling/SysML-v2-Release)
for the metamodel.

## What's next

- [Using the Reify HTTP API](./using-the-reify-http-api.md) — feed
  this component with real project data.
- [Reading a Requirements (REQ) diagram](./reading-a-requirements-req-diagram.md)
- [Reading a Safety (SAF) diagram](./reading-a-safety-saf-diagram.md)
