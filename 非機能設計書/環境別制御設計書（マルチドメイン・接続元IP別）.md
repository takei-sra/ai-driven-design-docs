# 環境別制御設計書（マルチドメイン・接続元IP別）

## 1. 目的
- 各デプロイ環境（開発／検証／本番）で利用するドメインを限定し、不正アクセスのリスクを低減する。&#8203;:contentReference[oaicite:1]{index=1}  
- 接続元 IP アドレスによる追加のホワイトリスト制御を行い、セキュリティ強度を向上させる。&#8203;:contentReference[oaicite:2]{index=2}  
- Spring Profiles を活用して、環境ごとの設定管理と切り替えを容易にする。&#8203;:contentReference[oaicite:3]{index=3}  

## 2. 適用範囲
- Spring Boot マイクロサービスおよびモノリシックアプリケーション全体。&#8203;:contentReference[oaicite:4]{index=4}  
- Web アプリケーションのエンドポイント（URL パス）レベルでの制御。  
- Kubernetes／Docker 環境下でも動作するフィルター／Firewall 設定。  

## 3. 基本方針
1. **マルチドメイン制御**: `StrictHttpFirewall` を使い、許可済みドメインのみリクエストを受け付ける。&#8203;:contentReference[oaicite:5]{index=5}  
2. **IP 制御**: Spring Security の `hasIpAddress()` やカスタムフィルターで接続元を限定する。&#8203;:contentReference[oaicite:6]{index=6}  
3. **プロファイル分離**: Spring Profiles（application-{env}.properties／@Profile）で環境ごとに設定を分離する。&#8203;:contentReference[oaicite:7]{index=7}  

## 4. 環境別ドメイン制御

### 4.1 StrictHttpFirewall によるホスト名ホワイトリスト
Spring Security の `StrictHttpFirewall` を利用して、`Host` ヘッダー値が許可済みドメインのリストに含まれる場合のみリクエストを許可します。設定例は以下の通りです。&#8203;:contentReference[oaicite:8]{index=8}

```java
@Bean
public StrictHttpFirewall strictHttpFirewall() {
    StrictHttpFirewall firewall = new StrictHttpFirewall();
    Set<String> allowed = Set.of("dev.example.com", "staging.example.com", "www.example.com");
    firewall.setAllowedHostnames(header -> allowed.contains(header));
    return firewall;
}

@Override
public void configure(WebSecurity web) throws Exception {
    web.httpFirewall(strictHttpFirewall());
}
