# State-of-the-Map-2022
State of the Map 2022のアカデミックトラック発表に関するリポジトリ

---

# Proposal title
RaspberryPi デバイスを用いた OpenStreetMap プリインストールオフライン地図サーバ UNVT Portable の開発と課題  
Development of UNVT Portable, a map server with OpenStreetMap pre-installed using a RaspberryPi on a local network in offline situations during disasters.

# Abstract 1-2 sentences
「UNVT Portable」は、地図ホスティングサーバとして機能し、ローカルネットワーク内でウェブブラウザから自由にアクセスすることのできる RaspberryPi 用のパッケージです。

主に、激甚災害時のオフライン環境下でも機能することを想定しており、災害対策本部が設置される地方自治体の市役所などで、発災後のドローン空撮画像と予め用意されているOpenStreetMapや各種オープンデータのタイルデータセットを組み合わせた状況把握や災害対応トリアージをユースケースで活躍することが期待されます。

本研究では、現在入手されている RaspberryPi 4 デバイスを前提に、現実的に利用可能なスペックでの OpenStreetMap をベースとしたプリインストールデータセットの構築と、その格納方法、その他画像タイルデータなど、OSMと相互に組み合わせるべきデータセットのパターンを検討し、ベースマップ、ドローン空撮データ、オープンデータとして入手可能な主題図作成のための避難所やハザードマップデータの適切なストレージ配分を算出します。

加えて、地方自治体での実証実験を行い、セキュアな設定がされた業務端末での利用での課題を明らかにします。


"UNVT Portable" is a package for RaspberryPi that functions as a map hosting server and can be freely accessed from a web browser within a local network.

It is mainly intended to function in an offline environment during times of severe disaster, and is expected to play an active role in use cases such as at city halls of local governments where disaster response headquarters are set up to assess the situation and respond to disasters by combining aerial drone images taken after a disaster occurs with OpenStreetMap and various open data tile data sets prepared in advance. 

In this study, assuming the currently available RaspberryPi 4 device, we will implement a pre-installed dataset based on OpenStreetMap with realistically available specs, and investigate how to store it, and other image tile data, etc., that should be mutually combined with OSM. Examine patterns of datasets and calculate appropriate storage allocations for basemaps, drone aerial photography data, and shelter and hazard map data for thematic mapping available as open data.

In addition, we will conduct demonstration tests at local governments to identify issues in using the system on business terminals with secure settings.




# Description  800 and 1200 words.

## 1. BACKGROUND
UN Vector Tile Toolkit (https://github.com/un-vector-tile-toolkit/) は、UN Open GIS Initiative の下で設計されたオープンソースツールのパッケージで、国連地理空間情報サービスや各国政府のマッピング組織などの公共ベースマップ提供者が、最新のウェブ地図技術を活用してベースマップのベクタータイルを配信できるようにするためのものです。(Eom et al., 2017; M. A. Brovelli, 2021; UN Open GIS Initiative, 2022)

このツールキットは、既存の実績あるオープンソースソフトウェアを組み合わせたパッケージとなっており、高速で相互運用可能なベースマップのベクタータイルを生成、ホスト、スタイル設定、最適化し、様々なアプリケーションフレームワークで利用できます。(Fujimura et al., 2019, The United Nations Vector Tile Toolkit, 2022)

「UNVT Portable」は、地図ホスティングサーバとして機能し、ローカルネットワーク内でウェブブラウザから自由にアクセスすることのできる RaspberryPi 用のパッケージです。

主に、激甚災害時のオフライン環境下でも機能することを想定しており、災害対策本部が設置される地方自治体の市役所などで、発災後のドローン空撮画像と予め用意されているOpenStreetMapや各種オープンデータのタイルデータセットを組み合わてウェブブラウザでオーバーレイ表示できます。そのため、被災地全域の状況把握や迅速な救援・復興作業を効率よく行え、ユースケースで活躍することが期待されます。

## 2. PURPOSE
本研究では、現在入手されている RaspberryPi 4 デバイスを前提に、現実的に利用可能なスペックでの OpenStreetMap をベースとしたプリインストールデータセットの構築と、格納方法、その他画像タイルデータなど、OSMと相互に組み合わせるデータセットのパターンを検討し、ベースマップ、ドローン空撮データ、オープンデータとして入手可能な主題図作成のための避難所やハザードマップデータの適切なストレージ配分を算出します。

加えて、地方自治体での実証実験を行い、セキュアな設定がされた業務端末での利用での課題を明らかにします。

## 3. STRATEGY
我々の研究グループは日本国内の複数の自治体と災害協定を締結し、大規模災害時に迅速なドローン空撮画像を提供する情報支援活動を行っています。そのデータ提供プロセスにおいて、データ提供先である自治体側の情報リテラシーと利用できる端末の動作環境によって、必ずしもスムーズな地理空間情報の受け渡しが行えていない現状があります。

更に、2019年の台風災害では、大規模な停電やインターネット回線の不通といったインフラが麻痺する状況が発生するなど、オフライン環境下でも正しく機能する使いやすいウェブ地図のような仕組みが必要であると仮説を立て、その実効性を実証実験を通して確認します。

性能面だけでなく、費用面においても比較的安価で入手可能な RaspberryPi デバイスを前提に設計することで、本研究によって完成された UNVT Portable 端末を大量に配備し、将来発生するであろう大規模な自然災害に備えます。


## 4. DESIGN
全世界の OpenStreetMap データセットの効果的な分割方法についてはすでに藤村が提案しており、この手法は2022年現在においても有効であると考える。

そのうえで、RaspberryPi 4 を前提に、搭載可能な MicroSD カードの容量、読み込み/レンダリング速度の性能を比較評価し、一般的な Chromebook の価格帯である 300 USDの半額である 150 USDをコスト上限と定義する。

ウェブホスティングサーバーは現時点では Apache を用いているが、nginx 等のオープンソースツールを用いることも検討している。

ローカルネットワークでの通信は Wi-Fi を用いた無線接続を最優先に設計した。

UNVT Portable への接続は、QRコードによる自動 Wi-Fi 接続機能を利用し、各主題図の表示は、それぞれの主題図ごとに QRコードを用意するなど、デジタル端末に不慣れな自治体職員でも簡易に地理空間情報にアクセスできるようデザインを行った。


## 5. DEVELOPMENT

私たちは、データソースに応じて汎用性の高い複数のバリエーションのタイルデータセットを生成しました。具体的には、OpenStreetMap、SRTM、Landsat画像、および日本の行政から提供されるオープンな地理空間情報データを利用しています。

このユースケースでは、すべてのデータを RaspberryPi OS と UNVT Portable として実装された各種ウェブホスティング/Wi-Fiアクセスポイントツールと組み合わせ RaspberryPi 端末にプリインストールします。

この UNVT Portable 端末に Wi-Fi 経由で接続する端末は、スマートフォン、タブレット、PC端末などクロスプラットフォームで利用することを前提に実装を行いました。

このように実装した UNVT Portable を自治体へ持ち込み、実際の業務で効果的に機能するか実証実験を行う予定です。


## 6. CONCLUSION



## 7. ACKNOWLEDGMENTS



## 8. REFERENCES.

Eom, K.S., Arias, R., Brovelli, M.A., Criloux, G., Kang, H.K., Li, K.J., 2017, United Nations Open GIS initiative: the first year of activities, Geoingegneria Ambientale e Mineraria, 2017, 151(2), 5–8, ISSN: 11219041.

Fujimura, H., Martin Sanchez, O., Gonzalez Ferreiro, D., Kayama, Y., Hayashi, H., Iwasaki, N., Mugambi, F., Obukhov, T., Motojima, Y., and Sato, T., 2019, Design and development of the UN Vector Tile Toolkit, Int. Arch. Photogramm. Remote Sens. Spatial Inf. Sci., XLII-4/W14, 57–62, https://doi.org/10.5194/isprs-archives-XLII-4-W14-57-2019.

The United Nations Vector Tile Toolkit, 2022. https://github.com/un-vector-tile-toolkit (22 May 2022).

UN Open GIS Initiative, 2022a, UN Open GIS Initiative. http://www.unopengis.org/ (22 May 2022).

---

提出用 Description

提出用 Description

# 1. BACKGROUND
The UN Vector Tile Toolkit (https://github.com/un-vector-tile-toolkit/) is a package of open source tools designed under the UN Open GIS Initiative to enable UN Geospatial Information Services, national government It is designed to enable public basemap providers, such as mapping organizations, to leverage modern web mapping technologies to deliver basemap vector tiles. (Eom et al., 2017; M. A. Brovelli, 2021; UN Open GIS Initiative, 2022)

The UNVT toolkit is packaged with existing proven open source software to generate, host, style, and optimize fast, interoperable basemap vector tiles for use in a variety of application frameworks. (Fujimura et al., 2019, The United Nations Vector Tile Toolkit, 2022)

UNVT Portable is a package for RaspberryPi that acts as a vector/raster tile map hosting server and can be freely accessed from a web browser within a local network.

It is mainly intended to function in an offline environment in the event of a severe disaster, and can be used at local government offices where disaster crisis response headquarters are set up to combine aerial drone imagaries with OpenStreetMap and various open data sets that have been prepared in advance and overlay them on a web browser. The system can display an overlay of the images in a web browser. As a result, the system is expected to play an active role in use cases, enabling an efficient grasp of the situation throughout the disaster area and prompt relief and reconstruction work.

# 2. PURPOSE
In this study, assuming the currently available RaspberryPi 4 device, we will build a pre-installed dataset based on OpenStreetMap with realistically available specifications, and the storage method and other tiled dataset, etc., to be combined with OSM and each other. patterns, and calculate the appropriate storage allocation of basemaps, drone aerial photography data, and evacuation shelter and hazard map data for thematic mapping that is available as open data.

In addition, we will conduct demonstration tests at local governments to identify issues in using the system on business terminals with secure settings.

# 3. STRATEGY
Our research group has entered into disaster management agreements with several local governments in Japan, and is engaged in information support activities to provide prompt aerial drone photography in the event of a large-scale disaster. In the process of providing data, there is a current situation in which geospatial information is not always transferred smoothly, depending on the information literacy of the local governments to which the data is provided and the operating environment of the available terminals.

Furthermore, we hypothesize that an easy-to-use web map-like system that functions properly in an offline environment is needed because the infrastructure was paralyzed by the typhoon disaster in 2019, including large-scale power outages and Internet outages, and will confirm its effectiveness through demonstration experiments.

By designing on the premise of RaspberryPi devices, which are relatively inexpensive in terms of both performance and cost, the UNVT Portable terminal completed through this research will be deployed in large numbers to prepare for large-scale natural disasters that may occur in the future.

# 4. DESIGN
Fujimura, 2019 has already proposed an effective partitioning method for the worldwide OpenStreetMap dataset, and we think this method is still valid as of 2022.

Based on the assumption of a RaspberryPi 4, we will compare and evaluate the capacity of the MicroSD card that can be installed and the performance of reading/rendering speed, and define 150 USD as the upper cost limit, which is half the price of 300 USD, the price range of a typical Chromebook.

The web hosting server is currently Apache, but we are considering using open source tools such as nginx.

For local network communication, wireless connection using Wi-Fi was designed as the highest priority.

The connection to UNVT Portable uses an automatic Wi-Fi connection function with QR codes, and each thematic map is displayed with a QR code for each thematic map, etc., designed to allow even municipal staff unfamiliar with digital terminals to easily access geospatial information.

# 5. DEVELOPMENT
We generated multiple variations of tile datasets that are versatile depending on the data source. Specifically, we used OpenStreetMap, SRTM, Landsat imagery, and open geospatial data provided by the Japanese government.

In this use case, all data is pre-installed on a RaspberryPi device in combination with the RaspberryPi OS and various web hosting/Wi-Fi access point tools implemented as UNVT Portable.

The browsing device that connect to the UNVT Portable device via Wi-Fi are implemented for cross-platform use, including smartphones, tablets, and PC terminals.

We plan to bring the UNVT Portable implemented in this way to local governments and conduct demonstration tests to see if it functions effectively in actual operations.

# 6. CONCLUSION
The conclusions of this study will be presented on the day of SotM 2022.


# 7. REFERENCES
* Eom, K.S., Arias, R., Brovelli, M.A., Criloux, G., Kang, H.K., Li, K.J., 2017, United Nations Open GIS initiative: the first year of activities, Geoingegneria Ambientale e Mineraria, 2017, 151(2), 5–8, ISSN: 11219041.
* Fujimura, H., Martin Sanchez, O., Gonzalez Ferreiro, D., Kayama, Y., Hayashi, H., Iwasaki, N., Mugambi, F., Obukhov, T., Motojima, Y., and Sato, T., 2019, Design and development of the UN Vector Tile Toolkit, Int. Arch. Photogramm. Remote Sens. Spatial Inf. Sci., XLII-4/W14, 57–62, https://doi.org/10.5194/isprs-archives-XLII-4-W14-57-2019.
* The United Nations Vector Tile Toolkit, 2022. https://github.com/un-vector-tile-toolkit (22 May 2022).
* UN Open GIS Initiative, 2022a, UN Open GIS Initiative. http://www.unopengis.org/ (22 May 2022).



# Additional Speaker
Shogo Hirasawa
origami.no.1@gmail.com



