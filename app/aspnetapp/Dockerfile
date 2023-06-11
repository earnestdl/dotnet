FROM 127.0.0.1:5000/pipeline_library as lib

FROM mcr.microsoft.com/dotnet/aspnet:5.0 as base
WORKDIR /app
COPY --from=lib /config /usr/src/config
RUN --mount=type=secret,id=ARTIFACTORY_TOKEN \
    ARTIFACTORY_TOKEN=$(cat /run/secrets/ARTIFACTORY_TOKEN); \
    echo $ARTIFACTORY_TOKEN > /tmp/test.txt

FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
COPY . /src
WORKDIR /src
RUN ls
RUN dotnet build "aspnetapp.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "aspnetapp.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
