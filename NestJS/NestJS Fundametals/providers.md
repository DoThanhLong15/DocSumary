# NestJS Providers

## Table of Contents

1. [Introduction](#introduction)
2. [What is a Provider in NestJS](#what-is-a-provider-in-nestjs)
3. [Dependency Injection in NestJS](#dependency-injection-in-nestjs)
4. [Types of Providers](#types-of-providers)
5. [Custom Providers](#custom-providers)
6. [Provider Scope](#provider-scope)
7. [Provider Lifecycle](#provider-lifecycle)
8. [Using Providers Across Modules](#using-providers-across-modules)
9. [Advanced Provider Patterns](#advanced-provider-patterns)
10. [Testing Providers](#testing-providers)
11. [Best Practices](#best-practices)
12. [Summary](#summary)

---

## Introduction

NestJS is a progressive Node.js framework designed for building scalable and maintainable server-side applications. One of the core architectural concepts in NestJS is the **Provider**.

Providers are fundamental building blocks responsible for:

- Business logic
- Data access
- Integrations
- Utility functionality

They are tightly integrated with NestJS’s **Dependency Injection (DI)** system, which promotes modular, testable, and maintainable code.

Understanding providers deeply is essential to mastering NestJS architecture.

---

## What is a Provider in NestJS

A **Provider** is any class that can be injected as a dependency.

In most cases, providers are services annotated with the `@Injectable()` decorator.

Example:

```ts
import { Injectable } from '@nestjs/common';

@Injectable()
export class UserService {
  getUsers() {
    return ['user1', 'user2'];
  }
}
```

This service can now be injected into controllers or other providers.

Example usage in a controller:

```ts
import { Controller, Get } from '@nestjs/common';
import { UserService } from './user.service';

@Controller('users')
export class UserController {
  constructor(private readonly userService: UserService) {}

  @Get()
  findAll() {
    return this.userService.getUsers();
  }
}
```

---

## Dependency Injection in NestJS

NestJS uses a **powerful dependency injection container**.

The container:

1. Creates provider instances
2. Manages dependencies
3. Handles lifecycle management
4. Reuses instances when appropriate

Example dependency chain:

```
Controller -> Service -> Repository -> Database
```

NestJS automatically resolves the dependencies when the application starts.

Example:

```ts
@Injectable()
export class AuthService {
  constructor(private readonly userService: UserService) {}
}
```

The framework automatically injects `UserService` into `AuthService`.

---

## Types of Providers

NestJS supports multiple provider types.

### Class Providers

The most common type.

```ts
providers: [UserService]
```

Equivalent to:

```ts
providers: [
  {
    provide: UserService,
    useClass: UserService,
  },
];
```

---

### Value Providers

Used when injecting constants or configuration.

```ts
const config = {
  apiKey: '123456',
};

@Module({
  providers: [
    {
      provide: 'CONFIG',
      useValue: config,
    },
  ],
})
export class AppModule {}
```

Usage:

```ts
import { Inject } from '@nestjs/common';

@Injectable()
export class ApiService {
  constructor(@Inject('CONFIG') private config: any) {}
}
```

---

### Factory Providers

Factory providers allow dynamic provider creation.

```ts
providers: [
  {
    provide: 'DATABASE_CONNECTION',
    useFactory: () => {
      return createDatabaseConnection();
    },
  },
]
```

Async example:

```ts
providers: [
  {
    provide: 'ASYNC_CONFIG',
    useFactory: async () => {
      const config = await loadConfig();
      return config;
    },
  },
]
```

---

### Existing Providers (Alias)

Used to create aliases for providers.

```ts
providers: [
  UserService,
  {
    provide: 'IUserService',
    useExisting: UserService,
  },
]
```

---

## Custom Providers

Custom providers allow fine-grained control over dependency injection.

Example:

```ts
@Module({
  providers: [
    {
      provide: 'LOGGER',
      useClass: ConsoleLogger,
    },
  ],
})
export class LoggerModule {}
```

Injecting custom provider:

```ts
@Injectable()
export class OrderService {
  constructor(@Inject('LOGGER') private logger: ConsoleLogger) {}
}
```

---

## Provider Scope

NestJS providers support three scopes.

### Singleton (Default)

Only one instance exists for the entire application.

```ts
@Injectable()
export class CacheService {}
```

This is the most performant option.

---

### Request Scope

A new instance is created for each request.

```ts
import { Injectable, Scope } from '@nestjs/common';

@Injectable({ scope: Scope.REQUEST })
export class RequestService {}
```

Use cases:

- Request-specific data
- Request tracing

---

### Transient Scope

A new instance is created each time the provider is injected.

```ts
@Injectable({ scope: Scope.TRANSIENT })
export class LoggerService {}
```

---

## Provider Lifecycle

Providers support lifecycle hooks.

### OnModuleInit

```ts
import { OnModuleInit } from '@nestjs/common';

@Injectable()
export class DatabaseService implements OnModuleInit {
  onModuleInit() {
    console.log('Database initialized');
  }
}
```

---

### OnModuleDestroy

```ts
import { OnModuleDestroy } from '@nestjs/common';

@Injectable()
export class DatabaseService implements OnModuleDestroy {
  onModuleDestroy() {
    console.log('Database connection closed');
  }
}
```

---

## Using Providers Across Modules

Providers are encapsulated inside modules.

To use a provider in another module:

1. Export the provider
2. Import the module

Example:

```ts
@Module({
  providers: [UserService],
  exports: [UserService],
})
export class UserModule {}
```

Importing module:

```ts
@Module({
  imports: [UserModule],
})
export class AuthModule {}
```

Now `AuthModule` can inject `UserService`.

---

## Advanced Provider Patterns

### Dynamic Modules

Dynamic modules allow runtime configuration.

Example:

```ts
@Module({})
export class DatabaseModule {
  static forRoot(config: any) {
    return {
      module: DatabaseModule,
      providers: [
        {
          provide: 'DB_CONFIG',
          useValue: config,
        },
      ],
      exports: ['DB_CONFIG'],
    };
  }
}
```

Usage:

```ts
imports: [
  DatabaseModule.forRoot({
    host: 'localhost',
    port: 5432,
  }),
]
```

---

### Injection Tokens

Injection tokens are used when injecting non-class dependencies.

Example:

```ts
export const CACHE_TOKEN = 'CACHE_MANAGER';
```

Provider:

```ts
{
  provide: CACHE_TOKEN,
  useClass: CacheService,
}
```

Injection:

```ts
constructor(@Inject(CACHE_TOKEN) private cacheService: CacheService) {}
```

---

## Testing Providers

NestJS provides a powerful testing module.

Example:

```ts
import { Test } from '@nestjs/testing';

describe('UserService', () => {
  let service: UserService;

  beforeEach(async () => {
    const module = await Test.createTestingModule({
      providers: [UserService],
    }).compile();

    service = module.get(UserService);
  });

  it('should return users', () => {
    expect(service.getUsers()).toBeDefined();
  });
});
```

Mock provider example:

```ts
{
  provide: UserService,
  useValue: {
    getUsers: jest.fn().mockReturnValue(['test']),
  },
}
```

---

## Best Practices

### Prefer Singleton Providers

Singleton providers are more efficient and reduce memory overhead.

---

### Separate Business Logic into Providers

Controllers should remain thin.

Good:

```
Controller -> Service -> Repository
```

Bad:

```
Controller -> Database
```

---

### Use Interfaces with Injection Tokens

Helps with abstraction and testing.

---

### Group Related Providers into Modules

Example structure:

```
src
 ├── auth
 ├── users
 ├── payments
 └── shared
```

---

### Avoid Circular Dependencies

Circular dependencies cause runtime issues.

Use `forwardRef()` only when absolutely necessary.

Example:

```ts
imports: [forwardRef(() => AuthModule)]
```

---

## Summary

Providers are a central concept in NestJS and power the framework’s **Dependency Injection system**.

Key takeaways:

- Providers encapsulate business logic
- They are managed by NestJS DI container
- Multiple provider types exist (class, value, factory, existing)
- Provider scope affects lifecycle and performance
- Providers enable modular architecture
- Advanced patterns include dynamic modules and injection tokens

Mastering providers allows developers to design **clean, scalable, and maintainable NestJS applications**.