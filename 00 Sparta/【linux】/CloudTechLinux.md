# シェルの概念や特徴(動画で学習、cron以降はインフラに触るようになってからでいいと思う)
## パーミッション
![スクリーン ショット 2023-04-21 に 14 35 17 午後](https://user-images.githubusercontent.com/81806676/233548902-13f4f6a4-f81f-41cb-96dd-d76f753f6809.png)
![スクリーン ショット 2023-04-21 に 14 36 40 午後](https://user-images.githubusercontent.com/81806676/233549088-628ef23e-2a30-4b5d-80a3-7791104bae77.png)

# 運用・保守
## SSH接続
### SSHデーモンとSSHクライアントのどちらも起動していないと接続できない。
![スクリーン ショット 2023-04-21 に 14 59 02 午後](https://user-images.githubusercontent.com/81806676/233552297-e4625061-3099-4b5a-9641-ba4ec074ad53.png)

## cron
### cronとは
![スクリーン ショット 2023-04-21 に 15 08 08 午後](https://user-images.githubusercontent.com/81806676/233553778-d07a2792-178e-4f2d-93ca-95e54a8381cb.png)
### cronの書き方
![スクリーン ショット 2023-04-21 に 15 10 11 午後](https://user-images.githubusercontent.com/81806676/233554122-eed9aadd-3ba3-4a1b-ade9-18c63ead891f.png)
![スクリーン ショット 2023-04-21 に 15 12 49 午後](https://user-images.githubusercontent.com/81806676/233554670-66ab7560-dff1-4861-a707-6cbeeb2675f5.png)
### cronの設定
![スクリーン ショット 2023-04-21 に 15 14 07 午後](https://user-images.githubusercontent.com/81806676/233555069-1300a91b-f3a7-4ebe-8894-e92ea4596530.png)
### AWSでのcronの使用
![スクリーン ショット 2023-04-21 に 15 16 08 午後](https://user-images.githubusercontent.com/81806676/233555602-cacdb3ea-3687-482d-914b-e6bb946db154.png)



# コマンド
## パフォーマンスコマンド
### top
  ![スクリーン ショット 2023-04-19 に 19 46 09 午後](https://user-images.githubusercontent.com/81806676/233051826-d16665ef-5b12-4230-a85f-31e4056ff25c.png)
  ![スクリーン ショット 2023-04-19 に 19 49 45 午後](https://user-images.githubusercontent.com/81806676/233054825-fbce1fac-1df4-49db-a84c-23c2d9fe68d8.png)
  ![スクリーン ショット 2023-04-19 に 19 50 30 午後](https://user-images.githubusercontent.com/81806676/233054846-551c252d-2e40-4997-accc-eff37f25fc3d.png)
  ![スクリーン ショット 2023-04-19 に 19 54 20 午後](https://user-images.githubusercontent.com/81806676/233054879-1309dc5a-3dd5-4876-b9c6-6844371c1769.png)
  - free
    - 空きメモリが少ないことを示すわけではない。解放可能なメモリも増えているので、これを解放してあげると空きメモリができる。
### vmstat
![スクリーン ショット 2023-04-19 に 20 04 47 午後](https://user-images.githubusercontent.com/81806676/233056877-af4df872-c4e8-4d35-9f6e-fa23094ee978.png)

### sar
![スクリーン ショット 2023-04-19 に 20 06 23 午後](https://user-images.githubusercontent.com/81806676/233056867-474c49ef-6bfb-498f-b219-aea21985d321.png)

### netstat
![スクリーン ショット 2023-04-19 に 20 07 56 午後](https://user-images.githubusercontent.com/81806676/233056846-7f63a49b-b829-4f0d-bc74-0a62101906d7.png)

## ネット監視コマンド
### ping
ネットワークが繋がらなくなった時に使うといい。pingコマンドで返りがなければ通信経路に問題があると判定できる。
EC2においてはデフォルトでping通信ができないので設定が必要になる。
![スクリーン ショット 2023-04-19 に 20 11 10 午後](https://user-images.githubusercontent.com/81806676/233058526-33594cd9-f638-4c6b-a732-1ca723ac3622.png)
![スクリーン ショット 2023-04-19 に 20 14 53 午後](https://user-images.githubusercontent.com/81806676/233058490-cb407bee-c607-42b7-8501-b1943994a9fa.png)

### route
![スクリーン ショット 2023-04-19 に 20 16 11 午後](https://user-images.githubusercontent.com/81806676/233059285-bccbe636-185b-454a-87e9-ea2f2e8a4ce0.png)

### traceroute
![スクリーン ショット 2023-04-19 に 20 20 02 午後](https://user-images.githubusercontent.com/81806676/233060078-afc28a67-388d-46d2-829a-1191de458f39.png)

### netstat
![スクリーン ショット 2023-04-19 に 20 23 07 午後](https://user-images.githubusercontent.com/81806676/233061059-6f08b934-934f-4e21-b8b5-9552fc8eb547.png)

### nslookup/host/dig
nslookupは非推奨、詳細も確認できるのでdigを使うべし。
![スクリーン ショット 2023-04-19 に 20 24 57 午後](https://user-images.githubusercontent.com/81806676/233061022-a34114d9-5356-4283-9e34-8c1cbdece94a.png)





# Linuxの特徴/ディストリビューションについて
動画で学習してください。
