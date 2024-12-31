# デバッグ コンテナーをカスタマイズする方法と、Visual Studio がこの Dockerfile を使用してより高速なデバッグのためにイメージをビルドする方法については、https://aka.ms/customizecontainer をご覧ください。

# このステージは、VS から高速モードで実行するときに使用されます (デバッグ構成の既定値)
FROM mcr.microsoft.com/dotnet/aspnet:9.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443


# このステージは、サービス プロジェクトのビルドに使用されます
FROM mcr.microsoft.com/dotnet/sdk:9.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["Jp.Gele.OtameshiBlazor.EntryPoint/Jp.Gele.OtameshiBlazor.EntryPoint.csproj", "Jp.Gele.OtameshiBlazor.EntryPoint/"]
COPY ["Jp.Gele.OtameshiBlazor.EntryPoint/Jp.Gele.OtameshiBlazor.WebShared.csproj", "Jp.Gele.OtameshiBlazor.WebShared/"]
COPY ["Jp.Gele.OtameshiBlazor.EntryPoint/Jp.Gele.OtameshiBlazor.StandardPages.csproj", "Jp.Gele.OtameshiBlazor.StandardPages/"]
RUN dotnet restore "./Jp.Gele.OtameshiBlazor.EntryPoint/Jp.Gele.OtameshiBlazor.EntryPoint.csproj"
COPY . .
WORKDIR "/src/Jp.Gele.OtameshiBlazor.EntryPoint"
RUN dotnet build "./Jp.Gele.OtameshiBlazor.EntryPoint.csproj" -c $BUILD_CONFIGURATION -o /app/build

# このステージは、最終ステージにコピーするサービス プロジェクトを公開するために使用されます
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./Jp.Gele.OtameshiBlazor.EntryPoint.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# このステージは、運用環境または VS から通常モードで実行している場合に使用されます (デバッグ構成を使用しない場合の既定)
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Jp.Gele.OtameshiBlazor.EntryPoint.dll"]