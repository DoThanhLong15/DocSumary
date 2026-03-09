# NestJS Controllers

## Table of Contents

1. [Introduction](#1-introduction)
2. [Controller Fundamentals](#2-controller-fundamentals)
3. [Defining Controllers](#3-defining-controllers)
4. [Routing in Controllers](#4-routing-in-controllers)
5. [Request Handling](#5-request-handling)
6. [Request Parameters](#6-request-parameters)
7. [Request Body Handling](#7-request-body-handling)
8. [Query Parameters](#8-query-parameters)
9. [HTTP Status Codes and Responses](#9-http-status-codes-and-responses)
10. [Asynchronous Controllers](#10-asynchronous-controllers)
11. [Dependency Injection in Controllers](#11-dependency-injection-in-controllers)
12. [Validation in Controllers](#12-validation-in-controllers)
13. [DTO Usage in Controllers](#13-dto-usage-in-controllers)
14. [Exception Handling](#14-exception-handling)
15. [File Uploads](#15-file-uploads)
16. [Best Practices](#16-best-practices)
17. [Summary](#17-summary)

---

## 1. Introduction

Controllers are a core building block in a NestJS application. They are responsible for handling incoming HTTP requests and returning responses to the client.

In NestJS, controllers:

- Receive requests
- Process input
- Delegate business logic to services
- Return responses

Controllers should remain **thin**, delegating most logic to services.

Architecture overview:

```
Client → Controller → Service → Repository/Database
```

Controllers should not contain complex business logic.

---

## 2. Controller Fundamentals

A controller in NestJS is defined using the `@Controller()` decorator.

It groups related routes together and handles HTTP requests.

Key characteristics:

- Uses decorators
- Supports dependency injection
- Works with request lifecycle
- Integrates with pipes, guards, interceptors, and filters

Example request lifecycle:

```
Incoming Request
     ↓
Middleware
     ↓
Guards
     ↓
Interceptors (before)
     ↓
Pipes
     ↓
Controller Handler
     ↓
Service Layer
     ↓
Interceptors (after)
     ↓
Response
```

---

## 3. Defining Controllers

Basic controller example:

```ts
import { Controller, Get } from '@nestjs/common';

@Controller('users')
export class UsersController {

  @Get()
  findAll() {
    return 'Return all users';
  }

}
```

Explanation:

| Element | Description |
|------|------|
| `@Controller('users')` | Base route `/users` |
| `@Get()` | Handles HTTP GET requests |
| `findAll()` | Route handler |

Endpoint created:

```
GET /users
```

---

## 4. Routing in Controllers

NestJS supports all HTTP methods.

Common decorators:

| Decorator | HTTP Method |
|------|------|
| `@Get()` | GET |
| `@Post()` | POST |
| `@Put()` | PUT |
| `@Patch()` | PATCH |
| `@Delete()` | DELETE |
| `@Options()` | OPTIONS |
| `@Head()` | HEAD |
| `@All()` | All methods |

Example:

```ts
@Controller('products')
export class ProductsController {

  @Get()
  getProducts() {}

  @Post()
  createProduct() {}

  @Put(':id')
  updateProduct() {}

  @Delete(':id')
  deleteProduct() {}

}
```

Generated routes:

```
GET    /products
POST   /products
PUT    /products/:id
DELETE /products/:id
```

---

## 5. Request Handling

NestJS provides decorators to access request data.

Common decorators:

| Decorator | Description |
|------|------|
| `@Req()` | Raw request object |
| `@Res()` | Raw response object |
| `@Body()` | Request body |
| `@Param()` | Route parameters |
| `@Query()` | Query parameters |
| `@Headers()` | Request headers |
| `@Ip()` | Client IP |
| `@HostParam()` | Host parameters |

Example:

```ts
import { Controller, Get, Req } from '@nestjs/common';
import { Request } from 'express';

@Controller('info')
export class InfoController {

  @Get()
  getInfo(@Req() request: Request) {
    return {
      userAgent: request.headers['user-agent']
    };
  }

}
```

---

## 6. Request Parameters

Route parameters are extracted using `@Param()`.

Example:

```ts
@Controller('users')
export class UsersController {

  @Get(':id')
  getUser(@Param('id') id: string) {
    return `User ID: ${id}`;
  }

}
```

Request:

```
GET /users/42
```

Response:

```
User ID: 42
```

Extract multiple parameters:

```ts
@Get(':userId/orders/:orderId')
getOrder(
  @Param('userId') userId: string,
  @Param('orderId') orderId: string
) {
  return { userId, orderId };
}
```

---

## 7. Request Body Handling

Use `@Body()` to access request payload.

Example:

```ts
@Post()
createUser(@Body() body: any) {
  return body;
}
```

Example request:

```json
{
  "name": "John",
  "email": "john@example.com"
}
```

Better approach: use DTOs.

---

## 8. Query Parameters

Query parameters are accessed via `@Query()`.

Example:

```ts
@Get()
findUsers(@Query('page') page: number) {
  return `Page: ${page}`;
}
```

Request:

```
GET /users?page=2
```

Multiple parameters:

```ts
@Get()
findUsers(
  @Query('page') page: number,
  @Query('limit') limit: number
) {
  return { page, limit };
}
```

---

## 9. HTTP Status Codes and Responses

NestJS automatically returns status codes:

| Method | Default Status |
|------|------|
| GET | 200 |
| POST | 201 |
| DELETE | 200 |

Override status code:

```ts
import { HttpCode } from '@nestjs/common';

@Post()
@HttpCode(200)
createUser() {
  return { message: 'User created' };
}
```

Custom response headers:

```ts
import { Header } from '@nestjs/common';

@Get()
@Header('Cache-Control', 'none')
getData() {
  return {};
}
```

---

## 10. Asynchronous Controllers

Controllers often use async operations.

Example:

```ts
@Get(':id')
async getUser(@Param('id') id: string) {
  const user = await this.usersService.findById(id);
  return user;
}
```

NestJS supports:

- Promises
- Observables
- Async functions

Example returning Promise:

```ts
@Get()
findAll(): Promise<User[]> {
  return this.usersService.findAll();
}
```

---

## 11. Dependency Injection in Controllers

NestJS uses constructor-based dependency injection.

Example:

```ts
@Controller('users')
export class UsersController {

  constructor(private readonly usersService: UsersService) {}

  @Get()
  findAll() {
    return this.usersService.findAll();
  }

}
```

Benefits:

- Testability
- Loose coupling
- Separation of concerns

---

## 12. Validation in Controllers

NestJS integrates with `class-validator`.

Example DTO validation:

```ts
import { IsEmail, IsString } from 'class-validator';

export class CreateUserDto {

  @IsString()
  name: string;

  @IsEmail()
  email: string;

}
```

Controller usage:

```ts
@Post()
createUser(@Body() dto: CreateUserDto) {
  return dto;
}
```

Enable global validation pipe:

```ts
app.useGlobalPipes(new ValidationPipe());
```

---

## 13. DTO Usage in Controllers

DTO = Data Transfer Object.

Benefits:

- Validation
- Type safety
- Clean contracts

Example DTO:

```ts
export class CreateProductDto {
  name: string
  price: number
}
```

Controller usage:

```ts
@Post()
create(@Body() dto: CreateProductDto) {
  return this.productsService.create(dto)
}
```

---

## 14. Exception Handling

NestJS provides built-in exceptions.

Example:

```ts
import { NotFoundException } from '@nestjs/common';

@Get(':id')
getUser(@Param('id') id: string) {
  const user = this.usersService.findById(id);

  if (!user) {
    throw new NotFoundException('User not found');
  }

  return user;
}
```

Common exceptions:

| Exception | Status |
|------|------|
| BadRequestException | 400 |
| UnauthorizedException | 401 |
| ForbiddenException | 403 |
| NotFoundException | 404 |

---

## 15. File Uploads

NestJS integrates with `multer`.

Example:

```ts
@Post('upload')
@UseInterceptors(FileInterceptor('file'))
uploadFile(@UploadedFile() file: Express.Multer.File) {
  return file;
}
```

Example request:

```
POST /upload
Content-Type: multipart/form-data
```

File field:

```
file
```

---

## 16. Best Practices

### Keep Controllers Thin

Bad example:

```ts
@Post()
createUser() {
  // business logic here
}
```

Good example:

```ts
@Post()
createUser(@Body() dto: CreateUserDto) {
  return this.userService.create(dto);
}
```

---

### Use DTOs

Avoid raw objects.

Bad:

```ts
create(@Body() body: any)
```

Good:

```ts
create(@Body() dto: CreateUserDto)
```

---

### Use Validation Pipes

Always validate inputs.

```
ValidationPipe
```

---

### Avoid Using @Res()

Using `@Res()` bypasses Nest response handling.

Prefer returning objects.

Bad:

```ts
@Res() res
```

Good:

```ts
return { success: true }
```

---

### Use Proper HTTP Status Codes

Examples:

```
200 OK
201 Created
204 No Content
400 Bad Request
404 Not Found
```

---

### Organize Controllers by Domain

Recommended structure:

```
src
 ├── users
 │   ├── users.controller.ts
 │   ├── users.service.ts
 │   ├── dto
 │   └── entities
 ├── auth
 ├── products
```

---

## 17. Summary

NestJS controllers are responsible for handling incoming HTTP requests and returning responses.

Key concepts:

- Controllers define routes
- Decorators map HTTP methods
- DTOs ensure validation and type safety
- Services contain business logic
- Dependency injection promotes modular architecture

Production-ready controllers should follow these principles:

- Thin controllers
- Strong validation
- Clear separation of concerns
- Consistent API responses
- Proper exception handling

By following these practices, NestJS applications become scalable, maintainable, and production-ready.