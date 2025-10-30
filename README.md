<div align="center">
  <img src="https://github.com/ollamaflow/ollamaflow/raw/main/assets/logo.png" width="256" height="256">
</div>

# OllamaFlow.Sdk

**A C# SDK for interacting with OllamaFlow server instances – providing Ollama and OpenAI compatible API wrappers plus Frontend/Backend management.**

<p align="center">
  <img src="https://img.shields.io/badge/.NET-5C2D91?style=for-the-badge&logo=.net&logoColor=white" />
  <img src="https://img.shields.io/badge/C%23-239120?style=for-the-badge&logo=c-sharp&logoColor=white" />
  <img src="https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge" />
</p>

<p align="center">
  <a href="https://www.nuget.org/packages/OllamaFlow.Sdk/">
    <img src="https://img.shields.io/nuget/v/OllamaFlow.Sdk.svg?style=flat" alt="NuGet Version">
  </a>
  &nbsp;
  <a href="https://www.nuget.org/packages/OllamaFlow.Sdk">
    <img src="https://img.shields.io/nuget/dt/OllamaFlow.Sdk.svg" alt="NuGet Downloads">
  </a>
</p>

<p align="center">
  <strong>A .NET SDK for OllamaFlow – Intelligent load balancing and transformation with Ollama and OpenAI compatible APIs</strong>
</p>

<p align="center">
  Embeddings • Completions • Chat • Streaming • Frontend/Backend Admin
</p>

**IMPORTANT** – OllamaFlow.Sdk assumes you have deployed the OllamaFlow REST server. If you are integrating an OllamaFlow.Core library directly into your code, use of this SDK is not necessary.


---

## 🚀 Features

- **Ollama API Compatibility** – Generate, Chat, Embeddings, Model management
- **OpenAI API Compatibility** – Completions, Chat Completions, Embeddings
- **Admin APIs** – Manage Frontends and Backends (create/update/retrieve/delete, health)
- **Streaming Support** – Real-time streaming for Ollama completions and chat
- **Async/Await** – Fully asynchronous APIs
- **Configurable Logging** – Request/response logging with pluggable logger

## 📦 Installation

Install via NuGet:

```bash
dotnet add package OllamaFlow.Sdk
```

Or via Package Manager Console:

```powershell
Install-Package OllamaFlow.Sdk
```

## 🚀 Quick Start

### Basic Usage

```csharp
using OllamaFlow.Sdk;
using OllamaFlow.Core.Models.Ollama;

var sdk = new OllamaFlowSdk("http://localhost:43411");

// List available local models (Ollama-compatible backends)
var models = await sdk.Ollama.ListLocalModels();
Console.WriteLine($"Found {models?.Count ?? 0} models");

// Generate a text completion
var completionReq = new OllamaGenerateCompletion
{
    Model = "llama2",
    Prompt = "The meaning of life is",
    Options = new OllamaCompletionOptions
    {
        Temperature = 0.7f,
        NumPredict = 64
    }
};

var completion = await sdk.Ollama.GenerateCompletion(completionReq);
Console.WriteLine($"Completion: {completion?.Response}");
```

### With Logging

```csharp
var sdk = new OllamaFlowSdk("http://localhost:43411");
sdk.LogRequests = true;
sdk.LogResponses = true;
sdk.Logger = (level, message) => Console.WriteLine($"[{level}] {message}");
```

## 📖 API Overview

### Ollama API Methods

- `ListLocalModels()` – List models available on Ollama-compatible backends
- `ListRunningModels()` – List running models
- `PullModel(request)` – Stream model pull progress
- `DeleteModel(request)` – Delete a model
- `ShowModelInfo(request)` – Show model details
- `GenerateEmbeddings(request)` – Generate embeddings
- `GenerateCompletion(request)` / `GenerateCompletionStream(request)` – Text completions
- `GenerateChatCompletion(request)` / `GenerateChatCompletionStream(request)` – Chat completions

#### Streaming Completion

```csharp
await foreach (var chunk in sdk.Ollama.GenerateCompletionStream(completionReq))
{
    Console.Write(chunk.Response);
}
```

#### Streaming Chat

```csharp
using OllamaFlow.Core.Models.Ollama;

var chatReq = new OllamaGenerateChatCompletionRequest
{
    Model = "llama2",
    Messages = new()
    {
        new OllamaChatMessage { Role = "user", Content = "Tell me a joke" }
    },
    Options = new OllamaCompletionOptions { Temperature = 0.7f, NumPredict = 64 }
};

await foreach (var chunk in sdk.Ollama.GenerateChatCompletionStream(chatReq))
{
    Console.Write(chunk.Message?.Content);
}
```

### OpenAI-Compatible API Methods

- `GenerateCompletion(request)` – Text completions
- `GenerateChatCompletion(request)` – Chat completions
- `GenerateEmbeddings(request)` – Embeddings

```csharp
using OllamaFlow.Core.Models.OpenAI;

var request = new OpenAIGenerateChatCompletionRequest
{
    Model = "llama2",
    Messages = new()
    {
        new OpenAIChatMessage { Role = "user", Content = "Hello!" }
    },
    MaxTokens = 128,
    Temperature = 0.7f
};

var chat = await sdk.OpenAI.GenerateChatCompletion(request);
Console.WriteLine($"Assistant: {chat?.Choices?[0]?.Message?.Content}");
```

### Frontend Management

```csharp
// Retrieve all frontends
var frontends = await sdk.Frontend.RetrieveMany();

// Check if a frontend exists
bool exists = await sdk.Frontend.Exists("main-frontend");
```

### Backend Management

```csharp
// Retrieve all backends (with health)
var backends = await sdk.Backend.RetrieveMany();
var health = await sdk.Backend.RetrieveAllHealth();

// Create or update a backend
var backend = new OllamaFlow.Core.Backend { Identifier = "gpu-1", Hostname = "127.0.0.1", Port = 11434 };
var created = await sdk.Backend.Create(backend);
var updated = await sdk.Backend.Update("gpu-1", backend);
```

## ⚙️ Configuration

### Timeout

```csharp
var sdk = new OllamaFlowSdk("http://localhost:43411");
sdk.TimeoutMs = 120000; // 2 minutes
```

### Authentication (Admin APIs)

```csharp
var sdk = new OllamaFlowSdk("http://localhost:43411")
{
    BearerToken = "your-admin-token"
};
```

## ⚠️ Error Handling

All methods return null (or empty lists) on non-success responses and do not throw unless an argument is invalid. Use `Logger` and `LogRequests/LogResponses` for diagnostics.

## 📊 Version History

See CHANGELOG.md for release notes.

## 📄 License

This project is licensed under the MIT License.

