FROM mcr.microsoft.com/dotnet/aspnet:11.0 AS base
USER app
WORKDIR /app
EXPOSE 8080

FROM mcr.microsoft.com/dotnet/sdk:11.0 AS build
ARG BUILD_CONFIGURATION=Release
ARG BUILD_VERBOSITY=minimal
WORKDIR /src

COPY ["global.json", "nuget.config", "Directory.*.props", "./"]
COPY ["src/Sample.WebApi/Sample.WebApi.csproj", "src/Sample.WebApi/packages.lock.json", "./Sample.WebApi/"]
COPY ["src/Sample.Core/Sample.Core.csproj", "./Sample.Core/"]
COPY ["src/Sample.Infrastructure/Sample.Infrastructure.csproj", "./Sample.Infrastructure/"]

RUN dotnet restore "./Sample.WebApi/Sample.WebApi.csproj" \
    --verbosity $BUILD_VERBOSITY \
    --locked-mode

COPY src/ .

RUN dotnet publish "./Sample.WebApi/Sample.WebApi.csproj" \
    --no-restore \
    --configuration $BUILD_CONFIGURATION \
    --verbosity $BUILD_VERBOSITY \
    --output /app/publish \
    /p:UseAppHost=false

FROM base AS final
ARG ENVIRONMENT=Production
ENV ASPNETCORE_ENVIRONMENT=$ENVIRONMENT

WORKDIR /app
COPY --from=build /app/publish .
ENTRYPOINT ["dotnet", "Sample.WebApi.dll"]
