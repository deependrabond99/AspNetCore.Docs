---
title: gRPC services with C#
author: juntaoluo
description: Learn the basic concepts when writing gRPC services with C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/basics
---
# gRPC services with C\#

This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#. The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.

## proto file

gRPC uses a contract-first approach to API development. Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default. The *.proto* file contains:

* The definition of the gRPC service.
* The messages sent between clients and servers.

For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).

For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):

* Defines a `Greeter` service.
* The `Greeter` service defines a `SayHello` call.
* `SayHello` sends a `HelloRequest` message and receives a `HelloResponse` message:

[!code-proto[](~/tutorials//grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]

## Add a .proto file to a C\# app

The *.proto* file is included in a project by adding it to the `<Protobuf>` item group:

[!code-xml[](~/tutorials//grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-11)]

## C# Tooling support for .proto files

The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *.proto* files. The generated assets (files):

* Are generated on an as-needed basis each time the project is built.
* Aren't added to the project or checked into source control.
* Are a build artifact contained in the *obj* directory.

This package is required by both the server and client projects. `Grpc.Tools` can be added by using the Package Manager in Visual Studio or adding a `<PackageReference>` to the project file:

[!code-xml[](~/tutorials//grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=17)]

The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.

## Generated C# assets

The tooling package generates the C# types representing the messages defined in the included *.proto* files.

For server-side assets, an abstract service base type is generated. The base type contains the definitions of all the gRPC calls contained in the *.proto* file. Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls. For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated. A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.

[!code-csharp[](~/tutorials//grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

For client-side assets, a concrete client type is generated. The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called. For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated. Call `GreeterClient.SayHello` to initiate a gRPC call to the server.

[!code-csharp[](~/tutorials//grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?highlight=5-8&name=snippet)]

By default, server and client assets are generated for each *.proto* file included in the `<Protobuf>` item group. To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.

[!code-xml[](~/tutorials//grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-11)]

Similarly, the attribute is set to `Client` in client projects.

## Additional resources

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>
