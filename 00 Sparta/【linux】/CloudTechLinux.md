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
