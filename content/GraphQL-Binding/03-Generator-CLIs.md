# Generator CLIs

## `graphql-binding`

The `graphql-binding` generator CLI is used for code generation (_codegen_) in the context of GraphQL bindings. It can for example be used to generate TypeScript type definitions for your bindings.

### Install

Install with `npm`:

```sh
npm install -g graphql-binding
```
OR

Install with `yarn`:

```sh
yarn global add graphql-binding
```

### Usage

```
Usage: graphql-binding -i [input] -l [language] -b [outputBinding]

Options:
  --help                Show help                                      [boolean]
  --version             Show version number                            [boolean]
  --input, -i           Path to schema.js or schema.ts file            [string] [required]
  --language, -l        Language of the generator. Available languages:
                        typescript, javascript                         [string] [required]
  --outputBinding, -b   Output binding. Example: binding.ts            [string] [required]
  --outputTypedefs, -t  Output type defs. Example: typeDefs.graphql    [string]
```

### Usage with GraphQL Config

The `graphql-binding` CLI integrates with GraphQL Config. This means instead of passing arguments to the command, you can write a `.graphqlconfig.yml` file which will be read by the CLI.

For example, consider the following `.graphqlconfig.yml`:

```yaml
projects:
  myapp:
    schemaPath: schema.graphql
    extensions:
      codegen:
        - generator: graphql-binding
          language: typescript
          input: schema.js
          output:
            binding: mybinding.ts
```

Invoking simply `graphql codegen` in a directory where the above `.graphqlconfig` is available is equivalent to invoking the following terminal command:

```sh
graphql-binding \
  --language typescript \
  --input schema.js \
  --outputBinding mybinding.ts
```

## `prisma-binding`

### Install

Install with `npm`:

```sh
npm install -g prisma-binding
```

Install with `yarn`:

```sh
yarn global add prisma-binding
```

### Usage

```
Usage: prisma-binding -i [input] -l [language] -b [outputBinding]

Options:
  --help                Show help                                      [boolean]
  --version             Show version number                            [boolean]
  --input, -i           Path to prisma.graphql file                    [string] [required]
  --language, -l        Language of the generator. Available languages:
                        typescript, javascript                         [string] [required]
  --outputBinding, -b   Output binding. Example: binding.ts            [string] [required]
```

### Usage with GraphQL Config

The `prisma-binding` CLI integrates with GraphQL Config. This means instead of passing arguments to the command, you can write a `.graphqlconfig.yml` file which will be read by the CLI.

For example, consider the following `.graphqlconfig.yml`:

```yaml
projects:
  myapp:
    schemaPath: src/generated/prisma.graphql
    extensions:
      prisma: prisma/prisma.yml
      codegen:
        - generator: prisma-binding
          language: typescript
          output:
            binding: src/generated/prisma.ts
```

Invoking simply `graphql codegen` in a directory where the above `.graphqlconfig` is available is equivalent to invoking the following terminal command:

```sh
prisma-binding \
  --language typescript \
  --outputBinding mybinding.ts
```

### Upgrading from `prisma-binding` v1.X

 Versions lower than 2.0 of `prisma-binding` were based on the `graphql prepare` instead of the `graphql codegen` command. Here is how you need to update your Prisma project files to account for the changes:

 **prisma.yml**

```yml
# ... other properties

hooks:
  post-deploy:
    - graphql get-schema
    - graphql codegen
```

**.graphqlconfig.yml**

```yml
projects:
  myapp:
    schemaPath: src/generated/prisma.graphql
    extensions:
      prisma: prisma/prisma.yml
      codegen:
        - generator: prisma-binding
          language: typescript
          output:
            binding: src/generated/prisma.ts
```

## Writing your own generator CLI

If you're [adding custom functionality to your GraphQL binding](./04-Creating-your-own-binding.md#adding-custom-functionality-to-your-binding), you'll need to build a custom generator CLI for it.
