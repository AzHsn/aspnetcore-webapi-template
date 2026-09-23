#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:10.0 AS base
USER app
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["src/Presentations/WebApi/WebApi.csproj", "src/Presentations/WebApi/"]
COPY ["src/Libraries/Caching/Caching.csproj", "src/Libraries/Caching/"]
COPY ["src/Libraries/Models/Models.csproj", "src/Libraries/Models/"]
COPY ["src/Libraries/Core/Core.csproj", "src/Libraries/Core/"]
COPY ["src/Libraries/Data/Data.csproj", "src/Libraries/Data/"]
COPY ["src/Libraries/Services/Services.csproj", "src/Libraries/Services/"]
COPY ["src/Libraries/Data.Mongo/Data.Mongo.csproj", "src/Libraries/Data.Mongo/"]
COPY ["src/Libraries/Identity/Identity.csproj", "src/Libraries/Identity/"]

RUN dotnet restore "./src/Presentations/WebApi/WebApi.csproj"

COPY . .
WORKDIR "/src/Presentations/WebApi"
RUN dotnet build "./WebApi.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./WebApi.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "WebApi.dll"]