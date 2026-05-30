# ==========================================
# 1. ビルドステージ (Build Stage)
# ==========================================
# Java 21 を搭載したGradle公式イメージを使用
FROM gradle:8.7.0-jdk21 AS build
WORKDIR /home/gradle/project

# 先に設定ファイルだけをコピーして依存ライブラリをキャッシュ
COPY --chown=gradle:gradle backend/build.gradle ./
RUN gradle build --no-daemon -x test || true

# ソースコードをコピーしてビルド（テストはスキップ）
COPY --chown=gradle:gradle backend/src ./src
RUN gradle bootJar --no-daemon -x test

# ==========================================
# 2. 実行ステージ (Execution Stage)
# ==========================================
# 互換性が高く軽量な Eclipse Temurin の Java 21 JRE (slim版) を採用
FROM eclipse-temurin:21-jre-jammy
WORKDIR /app

# ビルドステージから完成したjarだけをコピー
COPY --from=build /home/gradle/project/build/libs/*.jar app.jar

# 第2段階のIssue 11（Oracle Wallet）の配置場所をあらかじめ用意
RUN mkdir -p /app/wallet

# Spring Bootの標準ポート
EXPOSE 8080

# 拡張性（$JAVA_OPTS）を持たせた起動コマンド
ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar app.jar"]
