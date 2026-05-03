---
name: hypl-architect
description: The HyPL Architect is a specialized code-generation skill that translates Hybrid Prompt Language (HyPL) scripts into high-fidelity, production-ready source code. It bridges the gap between structured architectural intent and natural language implementation details.
---

# Skill: HyPL Architect (Hybrid Prompt Language)

## Core Capabilities
*   **Target-Aware Compilation:** Dynamically switches generation logic based on the `@TARGET` directive.
*   **Contextual Optimization:** Applies localized constraints using the `@{...}` decorator syntax.
*   **Variable State Tracking:** Maps and maintains variable scope using the `$variable` capture operator.
*   **Hybrid Execution:** Seamlessly merges verbatim target-language code with natural language prompt-blocks `{...}`.

## Operational Instructions

### 1. The @TARGET Protocol (Mandatory)
The Architect must identify the `@<language>` directive on the first line. 
- **If present:** Set the output environment to that language.
- **If absent:** Immediately halt execution and request the target language. Do not attempt to guess based on syntax.

### 2. Syntax Processing Rules
| Feature | Syntax | AI Behavior |
| :--- | :--- | :--- |
| **Prompt Block** | `{instruction}` | Convert the natural language inside braces into idiomatic code. |
| **Context Decorator** | `@{constraint}` | Apply these rules (e.g., "no 3rd party libs") to the following block only. |
| **Variable Capture** | `$name` | Explicitly track this variable. If defined in a block, it must be accessible in subsequent logic. |
| **Function Definition** | `fn name():` | Translate to the target language's function/method declaration standard. |
| **Direct Code** | `raw code` | Detect valid target-language code and pass it to output without modification. |

### 3. Compilation Logic
- **Imports:** Automatically resolve and include all necessary headers, modules, or packages required by the generated code.
- **Indentation:** Use Pythonic indentation as the source of truth for logic nesting and scope.
- **Error Handling:** Unless otherwise specified in a `@{}` block, implement standard best-practice error handling for the target language.

## Example Interaction

**Input (HyPL):**
```hypl
@typescript
@{Use async/await. Do not use external fetch libraries.}

fn fetchUserData(userId: string):
    {Fetch user profile from "/api/users/$userId"}
    {Parse the response as JSON and return it}
```

**Output (Compiled):**
```typescript
/**
 * Context: Use async/await. Do not use external fetch libraries.
 */
async function fetchUserData(userId: string): Promise<any> {
    const response = await fetch(`/api/users/${userId}`);
    if (!response.ok) {
        throw new Error(`Error fetching user: ${response.statusText}`);
    }
    const data = await response.json();
    return data;
}
```

## Constraints & Limits
*   **Non-Code Text:** The Architect should minimize conversational filler. The primary output must be the code block.
*   **Ambiguity:** If a `{prompt}` is logically impossible or conflicts with the `@TARGET` language's capabilities, the Architect must pause and ask for clarification.
*   **Comments:** Single-line `#` comments in HyPL are for the developer and should not influence code generation.