# SIYA.md - SIYA Development Guidelines

## Project Overview

SIYA is a modular multi-agent AI system built as a Tauri desktop application with a TypeScript backend and vanilla HTML/CSS/JavaScript frontend. This document defines code style guidelines, review criteria, project-specific rules, and preferred patterns for maintaining consistency across the codebase.

## Technology Stack

- **Primary Language**: TypeScript (Node.js backend)
- **Desktop Framework**: Tauri (Rust-based)
- **Frontend**: HTML/CSS/JavaScript (custom component system)
- **Package Manager**: yarn
- **Build System**: TypeScript compiler + Tauri build system

## Code Style Guidelines

### TypeScript/JavaScript

#### Naming Conventions
- **Files**: kebab-case (`multi-agent.ts`, `browser-agent.ts`)
- **Classes**: PascalCase (`BaseAgent`, `ToolCollection`)
- **Interfaces**: PascalCase (`AgentStateData`, `ToolParameter`)
- **Methods**: camelCase (`updateMemory`, `processState`)
- **Variables**: camelCase (`currentStep`, `maxSteps`)
- **Constants**: UPPER_SNAKE_CASE (`MAX_RETRIES`, `DEFAULT_TIMEOUT`)

#### Class Structure
```typescript
export abstract class BaseAgent {
  /** JSDoc documentation for all public properties */
  name: string;
  description?: string;
  
  /** Constructor with destructured parameters */
  constructor({
    name,
    description,
    maxSteps = 10,
  }: {
    name: string;
    description?: string;
    maxSteps?: number;
  }) {
    this.name = name;
    this.description = description;
  }
  
  /** Abstract methods that must be implemented */
  abstract step(): Promise<string>;
  
  /** Protected methods for subclass use */
  protected abstract processState(state: AgentStateData): Promise<AgentStateData>;
}
```

#### Import/Export Patterns
```typescript
// Prefer named imports with explicit paths
import { AgentState, AgentStateData, Memory } from '../schema';
import { LLM } from '../llm';
import log from '../utils/logger';

// Use default exports for main classes
export default BaseAgent;

// Use named exports for utilities and types
export { ToolCollection, ToolParameter };
```

#### Error Handling
```typescript
// Use try-catch with detailed error messages
try {
  await this.withState(AgentState.RUNNING, async () => {
    // Main logic
  });
} catch (error) {
  const errorMessage = `Error during execution: ${(error as Error).message}`;
  log.error(errorMessage);
  throw new Error(errorMessage);
}

// Use type assertion for error handling
catch (error) {
  log.error(`Error in processing: ${(error as Error).message}`);
  state.error = error as Error;
  return state;
}
```

#### Interface Definitions
```typescript
// Clear, descriptive interfaces
export interface ToolParameter {
  type: 'string' | 'number' | 'boolean' | 'object' | 'array';
  description: string;
  required?: boolean | string[];
  enum?: string[];
  default?: any;
}

// Use enums for constants
export enum AgentState {
  IDLE = 'idle',
  RUNNING = 'running',
  FINISHED = 'finished',
  ERROR = 'error',
}
```

### Rust/Tauri

#### Naming Conventions
- **Functions**: snake_case (`get_platform_info`, `download_file`)
- **Variables**: snake_case (`downloads_dir`, `is_mobile`)
- **Types/Structs**: PascalCase (`AppConfig`, `ServerProcess`)
- **Constants**: UPPER_SNAKE_CASE (`MAX_RETRIES`, `DEFAULT_PORT`)

#### Tauri Commands
```rust
#[tauri::command]
async fn download_file(url: String, filename: String) -> Result<String, String> {
    let downloads_dir = dirs::download_dir()
        .ok_or_else(|| "Could not find downloads directory".to_string())?;
    
    // Implementation with proper error handling
    match std::fs::File::create(&file_path) {
        Ok(mut file) => {
            // Success handling
        }
        Err(e) => {
            let msg = format!("Failed to create file: {}", e);
            write_log(&msg);
            return Err(msg);
        }
    }
}
```

#### Platform-Specific Code
```rust
// Use conditional compilation for platform differences
#[cfg(target_os = "windows")]
use std::os::windows::process::CommandExt;

#[cfg(not(target_os = "windows"))]
use signal_hook::{consts::SIGTERM, consts::SIGINT};

// Runtime platform detection
let is_mobile = cfg!(target_os = "ios") || cfg!(target_os = "android");
```

### Frontend (HTML/CSS/JavaScript)

#### HTML Structure
```html
<!-- Use semantic HTML5 elements -->
<main class="chat-container">
  <section class="messages-section">
    <div class="message-list" role="log" aria-live="polite">
      <!-- Messages -->
    </div>
  </section>
</main>

<!-- Component containers -->
<div id="model-selector-container"></div>
<div id="history-sidebar-container"></div>
```

#### CSS Organization
```css
/* CSS Custom Properties for theming */
:root {
  --background-color: #fafafa;
  --text-color: #1a1a1a;
  --border-color: #e0e0e0;
  --card-bg: #ffffff;
}

/* Component-based naming */
.model-selector {
  /* Base styles */
}

.model-selector:hover {
  /* Hover states */
}

.model-selector.open {
  /* State modifiers */
}
```

#### JavaScript Component Pattern
```javascript
class ModelSelector {
  constructor() {
    this.models = [];
    this.selectedModelId = null;
    this.isInitialized = false;
  }
  
  async init() {
    await this.loadTemplate();
    this.setupEventListeners();
    await this.loadModels();
  }
  
  setupEventListeners() {
    // Event delegation pattern
    this.container.addEventListener('click', this.handleClick.bind(this));
  }
  
  async loadModels() {
    try {
      const response = await fetch('/api/models');
      this.models = await response.json();
    } catch (error) {
      console.error('Failed to load models:', error);
    }
  }
}
```

## File Organization

### Directory Structure
```
src/
├── agent/           # Agent implementations
├── api/            # Express.js API routes
├── llm/            # LLM provider implementations
├── tool/           # Tool implementations
├── utils/          # Utility functions
├── services/       # Application services
├── models/         # Database models
├── config/         # Configuration management
└── schema.ts       # Type definitions

src-tauri/
├── src/
│   ├── main.rs     # Desktop entry point
│   └── lib.rs      # Mobile library
├── Cargo.toml      # Rust configuration
└── tauri.conf.json # Tauri configuration

public/
├── chat-interface/
│   ├── index.html
│   ├── styles.css
│   ├── script.js
│   ├── components/  # HTML templates
│   ├── css/        # Component styles
│   └── js/         # Component scripts
└── images/         # Static assets
```

### File Naming
- **TypeScript files**: kebab-case with `.ts` extension
- **Test files**: `test-*.ts` or `*.test.ts`
- **Component files**: `component-name.html`, `component-name.css`, `component-name.js`
- **Configuration files**: lowercase with appropriate extensions

## Documentation Standards

### JSDoc Comments
```typescript
/**
 * Base class for all agents in the SIYA system.
 * Provides common functionality for state management, memory, and execution.
 * 
 * @example
 * ```typescript
 * class MyAgent extends BaseAgent {
 *   async step(): Promise<string> {
 *     return "Agent executed successfully";
 *   }
 * }
 * ```
 */
export abstract class BaseAgent {
  /**
   * The name of the agent
   */
  name: string;
  
  /**
   * Execute a single step of the agent's logic
   * @returns Promise that resolves to the step result
   * @throws Error if execution fails
   */
  abstract step(): Promise<string>;
}
```

### Code Comments
- Use inline comments for complex logic
- Explain "why" not "what"
- Keep comments up-to-date with code changes
- Use TODO comments for future improvements

## Testing Guidelines

### Test Structure
```typescript
// Integration test pattern
describe('MultiAgent', () => {
  let agent: MultiAgent;
  
  beforeEach(() => {
    agent = new MultiAgent({
      name: 'Test Agent',
      llm: mockLLM,
    });
  });
  
  it('should execute agents in sequence', async () => {
    const result = await agent.step();
    expect(result).toContain('success');
  });
});
```

### Test Scripts
- `npm test` - Run all tests
- `npm run test:browser` - Browser agent tests
- `npm run test:multi-agent` - Multi-agent tests
- `npm run test:swe` - Software engineering tests

## Build and Development

### Required Scripts
```json
{
  "scripts": {
    "build": "tsc && ./prepare-server.sh",
    "dev": "ts-node src/index.ts",
    "dev:desktop": "npm run prepare:desktop && tauri dev",
    "lint": "eslint .",
    "format": "prettier --write \"src/**/*.ts\"",
    "test": "jest"
  }
}
```

### TypeScript Configuration
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "strict": true,
    "strictNullChecks": false,
    "noImplicitAny": false,
    "sourceMap": true,
    "outDir": "dist",
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  }
}
```

## Code Review Criteria

### Must-Have Requirements
1. **Type Safety**: All TypeScript code must use proper types
2. **Error Handling**: All async operations must handle errors appropriately
3. **Documentation**: Public APIs must have JSDoc comments
4. **Testing**: New features must include appropriate tests
5. **Security**: No hardcoded secrets or insecure operations
6. **Performance**: Avoid blocking operations on the main thread

### Review Checklist
- [ ] Code follows naming conventions
- [ ] Proper error handling implemented
- [ ] JSDoc documentation for public APIs
- [ ] No console.log statements in production code
- [ ] Async/await used instead of .then()
- [ ] Proper resource cleanup (streams, timers, etc.)
- [ ] Platform-specific code properly isolated
- [ ] Security best practices followed

## Project-Specific Rules

### Agent Development
- All agents must extend `BaseAgent`
- Use `withState()` for state management
- Implement proper memory management
- Log all agent actions using the logger

### Tool Development
- All tools must extend `BaseTool`
- Implement parameter validation
- Handle errors gracefully
- Support cancellation where appropriate

### Frontend Components
- Use the custom component system
- Implement proper cleanup in destroy methods
- Follow mobile-first responsive design
- Maintain accessibility standards

### LLM Integration
- Use the LLM abstraction layer
- Implement proper token counting
- Handle rate limiting appropriately
- Support multiple providers

## Development Workflow

### Before Committing
1. Run `npm run lint` to check code style
2. Run `npm run format` to format code
3. Run `npm test` to ensure tests pass
4. Run `npm run build` to verify build succeeds

### Pull Request Process
1. Create feature branch from `main`
2. Implement changes following guidelines
3. Add/update tests as needed
4. Update documentation if required
5. Submit PR with clear description

## Tools and Environment

### Required Tools
- Node.js >= 18.0.0
- Rust (latest stable)
- Tauri CLI
- TypeScript compiler

### Recommended Extensions (VS Code)
- TypeScript and JavaScript Language Features
- Rust Analyzer
- Prettier
- ESLint
- Tauri

### Environment Variables
- `OPENAI_API_KEY` - OpenAI API key
- `ANTHROPIC_API_KEY` - Anthropic API key
- `LOG_LEVEL` - Logging level (debug, info, warn, error)

## Security Guidelines

### API Keys and Secrets
- Never commit API keys to version control
- Use environment variables for sensitive data
- Implement proper key rotation
- Use secure storage for desktop apps

### Input Validation
- Validate all user inputs
- Sanitize file paths and names
- Prevent code injection attacks
- Use parameterized queries for database operations

### Cross-Platform Security
- Implement proper sandboxing
- Use Tauri's security features
- Validate all IPC communications
- Implement proper CSP headers

## Performance Guidelines

### TypeScript/Node.js
- Use async/await for non-blocking operations
- Implement proper streaming for large data
- Use connection pooling for databases
- Implement proper caching strategies

### Frontend
- Lazy load components when needed
- Use CSS transforms for animations
- Implement proper memory cleanup
- Optimize bundle size

### Rust/Tauri
- Use async for I/O operations
- Implement proper error propagation
- Use appropriate data structures
- Minimize allocations in hot paths

## Contributing Guidelines

### Getting Started
1. Fork the repository
2. Install dependencies: `npm install`
3. Set up development environment
4. Create feature branch
5. Make changes following guidelines
6. Submit pull request

### Code Quality
- Follow all style guidelines
- Add appropriate tests
- Update documentation
- Ensure CI passes

### Communication
- Use clear commit messages
- Provide detailed PR descriptions
- Respond to review feedback promptly
- Ask questions when uncertain

---

This document should be updated as the project evolves. All team members are expected to follow these guidelines to maintain code quality and consistency across the SIYA codebase.
