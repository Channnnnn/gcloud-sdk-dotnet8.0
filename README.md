# gcloud-sdk + dotnetcore-3.1

The base image for any project that aims to use .NET Core 3.1 together with gcloud SDK in the same container. This image can also be found at [panot/gcloud-sdk-dotnetcore3.1](https://hub.docker.com/r/panot/gcloud-sdk-dotnetcore3.1).

## Usage

gcloud provides several options to reach its API to manipulate configuration via Console, CLI and REST. For programmatically, REST always the best bet but it does not support all operations especially when we want to manage GKE (Google Kubernetes Engine). There is an open-source library [https://github.com/kubernetes-client/csharp](https://github.com/kubernetes-client/csharp) that made REST operation against Kubernetes instance easier with C# library. However, there is still some mandatory operations that only possible via the CLI like selecting and issuing token per cluster. Build image of .NET Core with gcloud SDK made this possible using `Process.Start`.

Sample usage of this base image.

```
FROM panot/gcloud-sdk-dotnetcore3.1 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/core/sdk:3.1 AS build
WORKDIR /src
COPY ["myProject.csproj", "./"]
RUN dotnet restore "myProject.csproj"
COPY . .
WORKDIR "/src/."
RUN dotnet build "myProject.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "myProject.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .

ENTRYPOINT ["dotnet", "myProject.dll"]
```


## License
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)