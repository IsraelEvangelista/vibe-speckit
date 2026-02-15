---
name: cli-development
description: Expert patterns for building production-ready Command Line Interfaces (CLIs) with proper argument parsing, configuration management, and user experience.
triggers:
  - keywords: ["cli", "command line", "commander", "oclif", "ink", "yargs", "args", "flags", "subcommand"]
  - file_patterns: ["**/cli/**", "**/bin/**", "**/commands/**"]
  - code_patterns: ["import.*commander", "require.*commander", "program.command", "yargs"]
---

# CLI Development

Build professional-grade Command Line Interface tools with proper architecture, user experience, and maintainability.

## Overview

CLIs remain the primary interface for developers and power users. A well-designed CLI balances discoverability, ergonomics, and robustness.

## Key Principles

1. **Discoverability first** - Users should understand the tool without reading docs
2. **Consistency** - Follow POSIX conventions and standard patterns
3. **Robust parsing** - Handle edge cases gracefully
4. **Great UX** - Progress indicators, colors, helpful error messages

## Architecture Patterns

### Command Structure

```typescript
// cli/src/commands/hello.ts
import { Command } from 'commander';
import chalk from 'chalk';

export const helloCommand = new Command('hello')
  .description('Say hello to the user')
  .argument('<name>', 'Name to greet')
  .option('-t, --times <number>', 'Number of times to greet', '1')
  .option('-u, --uppercase', 'Convert to uppercase')
  .action(async (name, options) => {
    const times = parseInt(options.times, 10);
    const greeting = options.uppercase ? name.toUpperCase() : name;
    
    for (let i = 0; i < times; i++) {
      console.log(chalk.green(`Hello, ${greeting}!`));
    }
  });
```

### Program Setup

```typescript
// cli/src/index.ts
import { Command } from 'commander';
import { helloCommand } from './commands/hello';
import { configCommand } from './commands/config';
import { version } from '../package.json';

const program = new Command()
  .name('my-cli')
  .description('A professional CLI tool')
  .version(version, '-v, --version', 'Display version number')
  .configureHelp({
    sortSubcommands: true,
    subcommandTerm: (cmd) => cmd.name() + ' [options] [arguments]'
  });

program.addCommand(helloCommand);
program.addCommand(configCommand);

// Global error handling
program.exitOverride();

try {
  await program.parseAsync(process.argv);
} catch (error) {
  if (error.code !== 'commander.help' && error.code !== 'commander.version') {
    console.error(chalk.red('Error:'), error.message);
    process.exit(1);
  }
}
```

## Configuration Management

```typescript
// cli/src/config/index.ts
import { cosmiconfigSync } from 'cosmiconfig';
import { z } from 'zod';

const ConfigSchema = z.object({
  apiUrl: z.string().url().default('https://api.example.com'),
  apiKey: z.string().optional(),
  defaultOutput: z.enum(['json', 'yaml', 'table']).default('table'),
  verbose: z.boolean().default(false)
});

export type Config = z.infer<typeof ConfigSchema>;

export function loadConfig(): Config {
  const explorer = cosmiconfigSync('mycli');
  const result = explorer.search();
  
  return ConfigSchema.parse(result?.config ?? {});
}
```

## User Experience Enhancements

### Progress Indicators

```typescript
import ora from 'ora';

const spinner = ora('Loading data...').start();

try {
  const result = await fetchData();
  spinner.succeed('Data loaded successfully');
  return result;
} catch (error) {
  spinner.fail(`Failed to load data: ${error.message}`);
  throw error;
}
```

### Interactive Prompts

```typescript
import inquirer from 'inquirer';

const answers = await inquirer.prompt([
  {
    type: 'input',
    name: 'projectName',
    message: 'Project name:',
    validate: (input) => input.length > 0 || 'Project name is required'
  },
  {
    type: 'list',
    name: 'template',
    message: 'Choose a template:',
    choices: ['basic', 'typescript', 'react', 'node']
  },
  {
    type: 'confirm',
    name: 'useGit',
    message: 'Initialize git repository?',
    default: true
  }
]);
```

### Output Formatting

```typescript
import Table from 'cli-table3';
import chalk from 'chalk';

function displayTable(data: Array<{ name: string; status: string; url: string }>) {
  const table = new Table({
    head: [chalk.bold('Name'), chalk.bold('Status'), chalk.bold('URL')],
    colWidths: [30, 15, 50]
  });
  
  data.forEach(row => {
    const status = row.status === 'active' 
      ? chalk.green('● Active')
      : chalk.red('● Inactive');
    
    table.push([row.name, status, row.url]);
  });
  
  console.log(table.toString());
}
```

## Best Practices

### Argument Validation

```typescript
program
  .argument('<port>', 'Port number', (value) => {
    const port = parseInt(value, 10);
    if (isNaN(port) || port < 1 || port > 65535) {
      throw new InvalidArgumentError('Port must be a number between 1 and 65535');
    }
    return port;
  })
```

### Environment Variables

```typescript
program
  .option('-k, --api-key <key>', 'API key')
  .hook('preAction', (thisCommand) => {
    const options = thisCommand.opts();
    // Fallback to environment variable
    if (!options.apiKey) {
      options.apiKey = process.env.MYCLI_API_KEY;
    }
  });
```

### Graceful Shutdown

```typescript
process.on('SIGINT', () => {
  console.log(chalk.yellow('\nInterrupted. Cleaning up...'));
  // Cleanup logic here
  process.exit(0);
});

process.on('SIGTERM', () => {
  console.log(chalk.yellow('Received SIGTERM. Exiting...'));
  process.exit(0);
});
```

## Testing CLIs

```typescript
import { execa } from 'execa';
import { describe, it, expect } from 'vitest';

describe('CLI Commands', () => {
  it('should display help', async () => {
    const { stdout } = await execa`node dist/cli.js --help`;
    expect(stdout).toContain('Usage:');
    expect(stdout).toContain('Commands:');
  });
  
  it('should handle unknown commands', async () => {
    await expect(
      execa`node dist/cli.js unknown-command`
    ).rejects.toThrow();
  });
  
  it('should validate required arguments', async () => {
    const { stderr } = await execa({ reject: false })`node dist/cli.js hello`;
    expect(stderr).toContain('required');
  });
});
```

## Recommended Libraries

| Purpose | Libraries |
|---------|-----------|
| Argument parsing | `commander`, `yargs`, `oclif` |
| Colors/styling | `chalk`, `kleur`, `ansi-colors` |
| Progress spinners | `ora`, `nanospinner` |
| Tables | `cli-table3`, `tty-table` |
| Prompts | `inquirer`, `enquirer`, `prompts` |
| Configuration | `cosmiconfig`, `conf` |
| Validation | `zod`, `joi`, `yup` |

## Examples

### Complete CLI with Subcommands

```typescript
#!/usr/bin/env node
import { Command } from 'commander';
import chalk from 'chalk';
import pkg from './package.json' assert { type: 'json' };

const cli = new Command()
  .name(pkg.name)
  .description(pkg.description)
  .version(pkg.version)
  .configureOutput({
    outputError: (str, write) => write(chalk.red(str))
  });

cli.command('init')
  .description('Initialize a new project')
  .argument('[name]', 'Project name')
  .option('-t, --template <name>', 'Template to use', 'default')
  .option('--skip-git', 'Skip git initialization')
  .action(async (name, options) => {
    // Implementation
  });

cli.command('build')
  .description('Build the project')
  .option('-w, --watch', 'Watch for changes')
  .option('-o, --output <dir>', 'Output directory', 'dist')
  .action(async (options) => {
    // Implementation
  });

cli.parse();
```

## Key Takeaways

1. Always provide `--help` and `--version`
2. Follow POSIX conventions for flags
3. Provide meaningful error messages
4. Support both interactive and non-interactive modes
5. Handle interrupts gracefully
6. Write tests for CLI behavior
7. Document with examples
