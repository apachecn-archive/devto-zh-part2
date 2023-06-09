# Menawan kembali Web

> 原文：<https://dev.to/k4ml/menawan-kembali-web-4p3j>

Visi asal Sir Tim Berners Lee apabila memperkenalkan world wide web (web) adalah untuk membangunkan satu platform terbuka yang membolehkan komputer-komputer yang berhubung melalui Internet berkongsi maklumat dengan cara yang mudah dan tanpa melalui entiti tunggal yang boleh mengawal apa yang boleh diterbitkan atau tidak.

Secara umumnya, visi tersebut sudah pun tercapai. Hari ini berjuta-juta laman web wujud di Internet, memaparkan pelbagai bentuk maklumat merangkumi pelbagai jenis bidang dan topik.

Namun begitu, realiti hari ini adalah, 2 billion pengguna Internet di seluruh dunia hanya memilih untuk berhubung dengan komputer yang dimiliki beberapa syarikat gergasi seperti Google dan Facebook.

2 billion bukan satu angka yang kecil. Ianya 1/4 daripada keseluruhan populasi planet ini. Melalui 2 billion ini, Google dan Facebook mampu menjana pendapatan 20 - 80 billion setahun.

Selain daripada menjana pendapatan korporat, 2 billion pengguna ini juga turut memberikan satu kuasa yang sangat besar, iaitu kuasa mempengaruhi bagaimana 1/4 daripada populasi dunia berfikir dan membuat keputusan. Ianya bukan calang-calang kuasa. Ianya kuasa yang boleh menentukan, contohnya siapakah yang boleh menjadi presiden negara paling berkuasa di dunia. Kuasa ini sangat besar sampaikan Facebook atau Google sendiri masih belum pasti, bagaimana mahu memanfaatkan sepenuhnya kuasa yang mereka ada.

Jadi bagaimana kita mahu menawan kembali web dan menjadikannya tidak berpusat sebagaimana visi asal penciptanya ?

Di kalangan penggiat IT, yang banyak diperkatakan sekarang adalah rangkaian media sosial yang berasaskan teknologi seumpama bitcoin, atau lebih tepat blockchain. Antara yang sudah wujud sejak beberapa tahun adalah rangkaian scuttlebutt atau ssb.

ssb adalah rangkaian kawara (p2p) yang menghubungkan storan individu dengan individu lain dalam rangkaian berasaskan gossip protocol. Setiap orang (komputer) dalam rangkaian ssb mempunyai sebuah jurnal yang boleh diikuti oleh kawan mereka, dan kawan kepada kawan mereka (friends-of-a-friends - foaf).

Kelebihan ssb adalah, jika kita berada dalam rangkaian yang sama, seperti di dalam wifi yang sama, jurnal di dalam storan kita boleh di"sync" secara terus, tanpa memerlukan sambungan Internet.

## 东拼西凑

Patchwork adalah antara aplikasi media sosial yang dibangunkan di atas platform ssb.

[![Patchwork](img/3694d8eac5cc50b8861831b0d19f7e07.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KfJU741m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://telegra.ph/file/d5cb20edb7f54f6882474.png)

Menggunakan Patchwork sebenarnya tidak jauh bezanya dengan laman sosial seperti Facebook. Kita boleh 'Follow' orang (dan sebaliknya), komen pada tulisan mereka atau menerbitkan tulisan kita sendiri. Cuma perlu diingat, tulisan tersebut hanya berada pada komputer kita sehingga ada orang lain yang sync tulisan tersebut ke komputer mereka.

Jadi tiada sebarang server berpusat dalam rangkaian ssb. Data kita boleh berada di mana-mana. Setiap data tersebut boleh dikenalpasti melalui ID yang unik, atau dalam istilah teknikal dipanggil hash. Tiada sebarang entiti tunggal yang boleh mengawal atau mengehad aliran data kita.

Setiap data yang kita terbitkan ke dalam rangkaian ssb akan ditandatangan secara digital, yang akan menghalang orang lain daripada membuat tulisan palsu yang kononnya daripada kita. Sudah tentu jika dia mampu mendapatkan private key di komputer kita, dia akan dapat menyamar sebagai kita. Jadi seperti mana bitcoin, private key mesti dilindungi sebaiknya.

## 烧杯浏览器

[Beaker Browser](https://beakerbrowser.com/) adalah browser khusus yang boleh digunakan untuk mencapai fail yang dikongsikan melalui protokol [Dat](https://datproject.org/) .

[![Fritter dat](img/d550e5348787306c29aef03fb4825988.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J0YtwEoD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yjlv692h4y0gtfmg8exq.png)

Di atas adalah contoh aplikasi web menyerupai Twitter ( [Fritter](https://fritter.hashbase.io/) ) yang boleh dicapai melalui BeakerBrowser. Menggunakan BeakerBrowser, kita boleh membina laman web sendiri yang boleh dicapai oleh pengguna BeakerBrowser yang lain tanpa memerlukan sebarang *hosting* !

Rangkaian ssb dan aplikasi Patchwork atau BeakerBrowser mempunyai potensi besar untuk menjadi alternatif kepada rangkaian berpusat seperti Facebook, Twitter, Whatsapp dan sebagainya. Semakin orang ramai sedar mereka hanyalah komoditi niaga para kapitalis, mereka akan mula mencari alternatif untuk berhubung tanpa perlu menggadaikan diri kepada mana-mana entiti yang berpaksikan keuntungan.