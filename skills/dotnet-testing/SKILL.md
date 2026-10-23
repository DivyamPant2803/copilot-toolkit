---
name: dotnet-testing
description: Conventions for writing .NET unit and integration tests (xUnit, NUnit or MSTest; EF Core; ASP.NET Core) in TDD style, including stubs and running a filtered set of tests. Use when writing or fixing C# tests.
---

# .NET testing

## First, match the repo
Open one existing test file near the code under test and copy: framework (xUnit `[Fact]`/`[Theory]`, NUnit `[Test]`, MSTest `[TestMethod]`), assertion library (FluentAssertions, Shouldly or built-in), mocking library (Moq, NSubstitute), naming pattern, folder layout and any base classes, builders or fixtures. **The repo's existing style wins over anything below.**

## Defaults when the repo has no clear pattern
- Naming: `MethodOrBehaviour_Condition_ExpectedResult`.
- Structure: Arrange / Act / Assert, separated by blank lines.
- One behaviour per test; use `[Theory]` + `[InlineData]` (or `[TestCase]`) for the same behaviour across inputs.

## What to test where
- **Domain and services:** unit tests. Mock only external boundaries (HTTP clients, queues, clock, blob storage), not your own domain classes.
- **EF Core queries:** prefer the repo's existing approach (SQLite in-memory, Testcontainers, or a test database). Avoid the EF InMemory provider for query behaviour; it doesn't enforce relational rules.
- **API endpoints:** integration tests with `WebApplicationFactory<Program>` if the repo has them: check status codes, response shape and authorisation (unauthenticated → 401, wrong user → 403/404).
- **Validation:** one test per rule, including the boundary.
- **Time:** inject a clock (`TimeProvider` on .NET 8+, or the repo's abstraction); never `DateTime.Now` in assertions.

## TDD stubs
When the type under test doesn't exist yet, create the smallest compiling shape:
```csharp
public Task<ExportResult> ExportAsync(ExportRequest request, CancellationToken ct)
    => throw new NotImplementedException(); // TODO(<iid>): implement
```
Register new services in DI only if an integration test needs it.

## Running only the new tests
```bash
dotnet test <TestProject>.csproj --filter "FullyQualifiedName~OrderExportTests"
# or by trait/category if the repo uses them:
dotnet test --filter "Category=Story482"
```
Read the failure message to confirm it's `NotImplementedException` or an assertion, not a setup error.
