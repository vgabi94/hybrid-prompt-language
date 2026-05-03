# HyPL (Hybrid Prompt Language)

HyPL is a lightweight, indentation-based pseudocode language designed to orchestrate Large Language Models (LLMs) for complex code generation. By combining the structural clarity of Python with the expressive power of natural language, HyPL allows developers to dictate program flow, architecture, and constraints while offloading the exact implementation details to the LLM.

## Getting Started

You can use HyPL by providing the specialized prompt to your LLM or by integrating it as a "skill" in your code editor.

### 1. Custom AI Agents
Create a custom "Gem" or "Agent" in your preferred LLM platform (e.g., ChatGPT, Gemini) and paste the content of `HyPL-specialized-prompt.md` into the instructions section.

### 2. Code Editor Integration
If your editor supports custom skills:
- Copy the `hypl-architect` skill from the `skills/` directory.
- Paste it into your editor's skill folder (e.g., `~/.copilot/skills/` for VS Code).
- Verify the installation by asking your AI to "list skills in system prompt". You should see `hypl-architect` in the list.

## Key Features
* **Indentation-Based:** Python-like syntax for maximum readability.
* **Hybrid Execution:** Mix exact target-language code, loose pseudocode, and natural language instructions.
* **Context-Aware:** Apply constraints and context decorators at the function or block level.
* **Flexible by Design:** Not a strict compiler standard. HyPL acts as a structured guide for LLMs; you can break the rules, use custom pseudocode, or inject raw target code as needed.

## Specification

### 1. The Target Directive (Required)
The very first instruction in any HyPL script must be the `@TARGET` directive. This tells the LLM which language it is compiling the HyPL script into. 

* **Syntax:** `@<language>` (e.g., `@python`, `@cpp`, `@rust`, `@typescript`)
* **Rule:** If the `@TARGET` directive is missing, the LLM must **STOP** generation and ask for clarification.

### 2. Variables and Assignments
Assignments can be strict values, type-hinted variables, or natural language prompts evaluated by the LLM.

* **Standard Assignment:** `name = value`
* **Prompt Assignment:** `name = {prompt}` (The LLM will generate code to fulfill the prompt and assign the result).
* **Type Hints (Optional):** Use a colon `:` to enforce a type constraint (e.g., `x: int = {Calculate the sum of user ages}`).

### 3. Functions
Functions can be declared using either `fn`. Prompts can be used as the entire function body or within the block.

* **Block Style:** 
  ```hypl
  fn process_data(data):
      # implementation
  ```
* **Inline Prompt Style:** `name(args) = {prompt}`

### 4. Control Flow
HyPL natively supports standard Python-style control flow structures. You can mix exact conditions with prompt-based conditions.

**Conditionals:**
```hypl
if condition:
    action
elif {the user is an admin}:
    action
else:
    action
```

**Loops:**
```hypl
for item in list:
    action

while {there are still unread messages}:
    action
```

### 5. Special Operators

HyPL relies on two primary prompt operators delimited by braces `{}`:

* **The Prompt Block `{...}`**
  Contains natural language instructions for the LLM to convert into the target source code. 
  *Example:* `{Connect to the PostgreSQL database using environment variables}`

* **The Context Declarator `@{...}`**
  Acts like a decorator. It applies broader instructions, rules, or constraints to the function or block immediately following it.
  *Example:* `@{Optimize for memory usage and avoid third-party libraries}`

* **The Variable Capture Operator `$`**
Used inside a `{prompt}` to declare or reference variables that exist in the HyPL scope.
    * **Declare/Capture:** Defining `$name` inside a prompt makes that variable available to be referenced later in the HyPL script.
    * **Reference:** Including `$name` inside a prompt tells the LLM to use an existing HyPL variable within that specific instruction.
*   *Example:* `{get $input from standard input}` allows you to use the variable later: `for line in input:`.

### 6. Flexible Syntax Rules
HyPL is designed for AI comprehension, not strict machine compilation. 
1. **Rule Breaking:** The specification rules are not exhaustive. Users can break them. The LLM will use its best judgment to interpret the intent.
2. **Implicit Imports:** The LLM will automatically include necessary imports and dependencies based on the prompts and context.
3. **Pseudocode:** You can use generic, undefined Python-style pseudocode. The LLM will figure it out.
4. **Verbatim Code:** You can write actual code in the target language at any time. The LLM will copy it verbatim without misinterpreting it.
5. **Clarification:** If the user's intent is genuinely ambiguous, the LLM will pause and ask for clarification.

### 7. Comments
Comments are used to provide internal documentation for the developer and are ignored by the LLM during the code generation process.

* **Single-line:** Use `#` for comments (Python-style).
* **Behavior:** Any text following `#` on a line will not be interpreted as code or instructions.

```hypl
# This is a comment and will be ignored
x = {Get user input} # This part is also a comment
```

## Example Usage

Here is an example of a HyPL script designed to generate a robust Python script for processing log files.
```hypl
@python

# Standard imports will be handled automatically by the LLM
log_path: str = "/var/log/syslog"

@{Use the standard library only. Add comprehensive error handling.}
fn parse_server_logs(filepath: str):
    {Safely open $filepath and read $file_content}
    
    error_logs = []
    
    for line in file_content:
        if {$line contains the word 'ERROR' or 'CRITICAL'}:
            parsed_line = {Extract the timestamp, severity, and message into a dictionary}
            error_logs.append(parsed_line)
            
    return error_logs

@{Ensure the JSON output is beautifully indented}
def export_to_json(data, output_path: str):
    {Write the data dictionary to output_path as a JSON file}

# Main execution flow using verbatim target-language code mixed with prompts
if __name__ == "__main__":
    logs = parse_server_logs(log_path)
    if {logs list is not empty}:
        export_to_json(logs, "errors.json")
        print("Export complete.")
    else:
        print("No errors found.")
```