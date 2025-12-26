---
title: RemoteIpValveとNginxを組み合わせたソースIP情報および社内外判定情報の取得の仕組み
last_modified_date: "2025-12-26"
parent: DGCPコンテナと従来環境におけるソースIPの取得と社内外の判定方法の差異
nav_order: 1
---

<br><br>

## RemoteIpValveとNginxを組み合わせたソースIP情報および社内外判定情報の取得の仕組み  

1. Tomcatの機能であるRemoteIPValveとクライアントIPの取得を目的としたクラウド環境での利用方式（TomcatとNginx）  
    RemoteIPValveとはプロキシ環境でTomcatを運用する上で、  
    クライアントの実際のIPアドレスやプロトコルの情報などをHTTPヘッダー（X-Forwarded-For, X-Forwarded-Protoなど）から算出し、  
    TomcatとTomcatに載せたアプリケーションが認識するIPアドレス情報を書き換えるTomcat固有の機能です。  
<br>
    この機能を利用するためには、RemoteIPValveの処理で必要なHTTPヘッダーを指定しますが  
    指定したヘッダーの値はRemoteIPValveによって値が書き換えられる仕様のため、  
    「x-forwarded-for」を指定した場合は従来のx-forwarded-forの仕様とは異なる値が入った状態でTomcatおよびアプリケーションに渡されます。  
<br>
    「x-forwarded-for」の値が書き換わった状態でTomcatやアプリケーションにわたることは望ましくないため、  
    Nginxにて「x-forwarded-for」の値と同じ内容をセットした別のHTTPヘッダー「x-Iaptom-Remote-Ip」を作成してリクエスト情報に付与し、  
    RemoteIPValveの処理で必要なHTTPヘッダーには「x-Iaptom-Remote-Ip」を指定して  
    このヘッダー情報を元にクライアントのIPアドレスを算出する方式を用いました。  
<br>
1. 社内外判定情報の取得を目的としたNginxの処理方式  
    通信のsrcIPがPublic Subnetのレンジに含まれているか否かでインターネット経由のALBかを判別し  
    インターネットと判別した場合にリクエスト情報にヘッダー「X-YMSK:True」を付与します。  　
<br><br>
1. 参考資料  
    https://tomcat.apache.org/tomcat-9.0-doc/api/org/apache/catalina/valves/RemoteIpValve.html
