FROM microsoft/dotnet:2.0-sdk
COPY . app
WORKDIR /app
RUN dotnet restore
RUN dotnet build
CMD dotnet run