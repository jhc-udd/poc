# https://hub.docker.com/_/microsoft-dotnet
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /source

# copy csproj and restore as distinct layers
#COPY MvcMovie5/*.csproj ./MvcMovie5/
#WORKDIR /source/MvcMovie
#RUN dotnet restore
# copy everything else and build app

COPY MvcMovie/. ./MvcMovie/
WORKDIR /source/MvcMovie
RUN dotnet publish -c release -o /app 

# final stage/image
#FROM mcr.microsoft.com/dotnet/sdk:5.0
FROM mcr.microsoft.com/dotnet/aspnet:5.0
WORKDIR /app
COPY --from=build /app ./
ENV ASPNETCORE_URLS=http://+:80  
EXPOSE 80
ENTRYPOINT ["dotnet", "MvcMovie.dll"]
