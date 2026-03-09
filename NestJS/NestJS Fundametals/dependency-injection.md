# NestJS Dependency Injection — Deep Dive Guide

## Table of Contents

1. [Introduction](#introduction)
2. [What Is Dependency Injection](#what-is-dependency-injection)
3. [How Dependency Injection Works in NestJS](#how-dependency-injection-works-in-nestjs)
4. [Providers in NestJS](#providers-in-nestjs)
5. [Registering Providers](#registering-providers)
6. [Constructor Injection](#constructor-injection)
7. [Custom Providers](#custom-providers)
8. [Provider Scopes](#provider-scopes)
9. [Injection Tokens](#injection-tokens)
10. [Factory Providers](#factory-providers)
11. [Async Providers](#async-providers)
12. [Dependency Injection Across Modules](#dependency-injection-across-modules)
13. [Circular Dependencies](#circular-dependencies)
14. [Real-World Example](#real-world-example)
15. [Best Practices](#best-practices)
16. [Summary](#summary)

---

## Introduction

Dependency Injection (DI) is one of the **core architectural patterns** used by NestJS.

NestJS uses a **powerful IoC (Inversion of Control) container** to manage the lifecycle and dependencies of application components.

DI enables:

- Loose coupling between components
- Easier testing
- Better code organization
- Scalable architecture

In NestJS, services, repositories, and utilities are typically registered as **providers** and injected where needed.

---

## What Is Dependency Injection

Dependency Injection is a design pattern where **dependencies are provided to a class rather than created inside the class**.

Without DI:

```ts
class UserService {
  private logger = new LoggerService();
}
```

With DI:

```ts
class UserService {
  constructor(private logger: LoggerService) {}
}
```

Benefits:

- Improved testability
- Better modularity
- Flexible configuration

---

## How Dependency Injection Works in NestJS

NestJS manages dependencies through its **internal dependency injection container**.

The container:

1. Registers providers
2. Resolves dependencies
3. Instantiates classes
4. Injects required dependencies

Example flow:

```
Controller
   ↓
Service
   ↓
Repository
   ↓
Database
```

NestJS automatically resolves the chain of dependencies.

---

## Providers in NestJS

A **provider** is any class that can be injected as a dependency.

Common provider types:

- Services
- Repositories
- Factories
- Helpers
- Configuration objects

Example provider:

```ts
import { Injectable } from '@nestjs/common';

@Injectable()
export class UsersService {
  findAll() {
    return [];
  }
}
```

The `@Injectable()` decorator registers the class with the DI system.

---

## Registering Providers

Providers must be registered inside a module.

Example:

```ts
import { Module } from '@nestjs/common';
import { UsersService } from './users.service';

@Module({
  providers: [UsersService],
})
export class UsersModule {}
```

Now `UsersService` can be injected into other classes within the module.

---

## Constructor Injection

NestJS primarily uses **constructor-based dependency injection**.

Example:

```ts
import { Controller } from '@nestjs/common';
import { UsersService } from './users.service';

@Controller('users')
export class UsersController {
  constructor(private usersService: UsersService) {}

  getUsers() {
    return this.usersService.findAll();
  }
}
```

NestJS automatically resolves `UsersService`.

---

## Custom Providers

NestJS supports custom provider definitions using the following patterns:

- `useClass`
- `useValue`
- `useExisting`
- `useFactory`

Example using `useValue`:

```ts
@Module({
  providers: [
    {
      provide: 'APP_NAME',
      useValue: 'Expense Manager',
    },
  ],
})
export class AppModule {}
```

Injecting the value:

```ts
import { Inject } from '@nestjs/common';

constructor(@Inject('APP_NAME') private appName: string) {}
```

---

## Provider Scopes

Providers in NestJS support three scopes.

### Singleton (Default)

One instance shared across the application.

```ts
@Injectable()
export class UsersService {}
```

### Request Scope

A new instance per request.

```ts
import { Scope, Injectable } from '@nestjs/common';

@Injectable({ scope: Scope.REQUEST })
export class RequestService {}
```

### Transient Scope

A new instance for each injection.

```ts
@Injectable({ scope: Scope.TRANSIENT })
export class LoggerService {}
```

---

## Injection Tokens

Injection tokens identify providers inside the DI container.

Types of tokens:

- Class
- String
- Symbol

Example using string token:

```ts
{
  provide: 'CONFIG',
  useValue: { port: 3000 }
}
```

Injecting:

```ts
constructor(@Inject('CONFIG') private config: any) {}
```

Symbols can also be used:

```ts
export const CONFIG_TOKEN = Symbol('CONFIG');
```

---

## Factory Providers

Factory providers create providers dynamically.

Example:

```ts
@Module({
  providers: [
    {
      provide: 'DATABASE_CONNECTION',
      useFactory: () => {
        return createConnection();
      },
    },
  ],
})
export class DatabaseModule {}
```

Factories are useful for:

- Database connections
- External APIs
- Configuration objects

---

## Async Providers

Some providers require asynchronous initialization.

Example:

```ts
@Module({
  providers: [
    {
      provide: 'DATABASE',
      useFactory: async () => {
        const connection = await connectDatabase();
        return connection;
      },
    },
  ],
})
export class DatabaseModule {}
```

Async providers are common for:

- Database initialization
- External services
- Configuration loading

---

## Dependency Injection Across Modules

Providers can be shared across modules using **exports**.

Example:

Users module:

```ts
@Module({
  providers: [UsersService],
  exports: [UsersService],
})
export class UsersModule {}
```

Auth module:

```ts
@Module({
  imports: [UsersModule],
})
export class AuthModule {}
```

Now `UsersService` can be injected into `AuthModule`.

---

## Circular Dependencies

Circular dependencies occur when two providers depend on each other.

Example:

```
AuthService -> UsersService
UsersService -> AuthService
```

Solution:

Use `forwardRef()`.

Example:

```ts
import { forwardRef, Inject } from '@nestjs/common';

constructor(
  @Inject(forwardRef(() => AuthService))
  private authService: AuthService,
) {}
```

This delays dependency resolution.

---

## Real-World Example

Example application structure:

```
src/
 ├── app.module.ts
 ├── auth/
 │   ├── auth.module.ts
 │   ├── auth.service.ts
 │   └── auth.controller.ts
 ├── users/
 │   ├── users.module.ts
 │   └── users.service.ts
 ├── database/
 │   └── database.module.ts
 └── common/
     └── logger.service.ts
```

Example injection chain:

```
AuthController
     ↓
AuthService
     ↓
UsersService
     ↓
DatabaseService
```

NestJS resolves all dependencies automatically.

---

## Best Practices

### 1. Prefer Constructor Injection

Constructor injection makes dependencies explicit and improves testability.

Avoid property injection.

---

### 2. Keep Providers Small

Providers should follow the **Single Responsibility Principle**.

Example:

Good:

```
UserService
AuthService
EmailService
```

Bad:

```
UserAuthEmailEverythingService
```

---

### 3. Use Interfaces with Tokens

Interfaces cannot be injected directly.

Use tokens instead.

Example:

```ts
export const USER_REPOSITORY = 'USER_REPOSITORY';
```

---

### 4. Avoid Excessive Request Scope

Request-scoped providers increase memory usage and reduce performance.

Prefer singleton providers whenever possible.

---

### 5. Group Providers by Domain

Organize providers inside **feature modules**.

Example:

```
auth/
users/
payments/
notifications/
```

---

## Summary

Dependency Injection is a **core design principle** of NestJS.

Key takeaways:

- NestJS uses an IoC container to manage dependencies
- Providers are the primary injectable components
- Dependencies are resolved automatically through constructor injection
- Custom providers allow flexible dependency definitions
- Factory and async providers enable dynamic initialization
- Providers can be shared across modules using exports

A well-designed DI architecture leads to:

- Maintainable systems
- Testable services
- Modular applications
- Scalable backend architecture

Mastering NestJS dependency injection is essential for building **production-ready backend systems**.