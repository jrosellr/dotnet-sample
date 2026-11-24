FROM mcr.microsoft.com/dotnet/aspnet:11.0 AS base
USER app
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

FROM mcr.microsoft.com/dotnet/sdk:11.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src

COPY ["src/**/Sample.*.csproj", "Directory.*.props", "./"]

RUN dotnet restore "Sample.WebApi.csproj"
COPY . .

# WORKDIR "/src/Sample.WebApi"
RUN dotnet build "Sample.WebApi.csproj" \
    --no-restore \
    -c $BUILD_CONFIGURATION \
    -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "Sample.WebApi.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Sample.WebApi.dll"]
