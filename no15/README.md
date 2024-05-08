Catatan: Pada saat melakukan benchmarking untuk yang pertama kali, web server Severny dan Stalber masih menggunakan port selain 80, yaitu 8001 dan 8002 untuk masing-masing web server.
# Round Robin
## Script (Mylta - Load Balancer)
```bash
echo ' # Default menggunakan Round Robin
 upstream myweb  {
        server 10.69.4.2; #IP Serverny
        server 10.69.4.3; #IP Stalber
        server 10.69.4.4; #IP Lipovka
 }

 server {
        listen 80;
        server_name _;

        location / {
        proxy_pass http://myweb;
        }
 }' > /etc/nginx/sites-enabled/lb-jarkom

 service nginx restart
```

### Benchmark
#### Severny
```
root@Gatka:~# ab -n 1000 -c 100 http://10.69.4.2:8001/
This is ApacheBench, Version 2.3 <$Revision: 1706008 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/
Benchmarking 10.69.4.2 (be patient)
Completed 100 requests
Completed 200 requests
Completed 300 requests
Completed 400 requests
Completed 500 requests
Completed 600 requests
Completed 700 requests
Completed 800 requests
Completed 900 requests
Completed 1000 requests
Finished 1000 requests

Server Software:        nginx/1.10.3
Server Hostname:        10.69.4.2
Server Port:            8001
Document Path:          /
Document Length:        172 bytes
Concurrency Level:      100
Time taken for tests:   0.697 seconds
Complete requests:      1000
Failed requests:        0
Total transferred:      318000 bytes
HTML transferred:       172000 bytes
Requests per second:    1435.31 [#/sec] (mean)
Time per request:       69.671 [ms] (mean)
Time per request:       0.697 [ms] (mean, across all concurrent requests)
Transfer rate:          445.73 [Kbytes/sec] received

Connection Times (ms)
            min mean[+/-sd] median max
Connect:    5   31 10.5 33 56
Processing: 11  35 7.8 35 56
Waiting:    11  35 7.8 35 55
Total:      17  67 15.1 67 95

Percentage of the requests served within a certain time (ms)
50% 67
66% 71
75% 75
80% 78
90% 85
95% 89
98% 91
99% 93
100% 95 (longest request)
```

#### Stalber
```
root@Gatka:~# ab -n 1000 -c 100 http://10.69.4.3:8002/
This is ApacheBench, Version 2.3 <$Revision: 1706008 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/
Benchmarking 10.69.4.3 (be patient)
Completed 100 requests
Completed 200 requests
Completed 300 requests
Completed 400 requests
Completed 500 requests
Completed 600 requests
Completed 700 requests
Completed 800 requests
Completed 900 requests
Completed 1000 requests
Finished 1000 requests

Server Hostname:        10.69.4.3
Server Port:            8002
Document Path:          /
Document Length:        172 bytes
Concurrency Level:      100
Time taken for tests:   0.670 seconds
Complete requests:      1000
Failed requests:        0
Total transferred:      318000 bytes
HTML transferred:       172000 bytes
Requests per second:    1491.55 [#/sec] (mean)
Time per request:       67.044 [ms] (mean)
Time per request:       0.670 [ms] (mean, across all concurrent requests)
Transfer rate:          463.20 [Kbytes/sec] received

Connection Times (ms)
            min mean[+/-sd] median max
Connect:    3 30 8.5 31 47
Processing: 11 34 5.9 35 46
Waiting:    11 34 6.0 35 46
Total:      16 64 11.3 66 80

Percentage of the requests served within a certain time (ms)
50% 66
66% 68
75% 70
80% 74
90% 76
95% 78
98% 79
99% 79
100% 80 (longest request)
```
Analisis: Berdasarkan benchmark berikut, Stalber menunjukkan performa yang lebih unggul dibandingkan Severny dalam hal throughput, kecepatan respons, transfer data, dan konsistensi dalam penanganan permintaan. Stalber tidak hanya lebih cepat dalam menyelesaikan 1000 requests tetapi juga menunjukkan waktu maksimal yang lebih pendek dan lebih konsisten dalam waktu respons median, yang membuatnya menjadi pilihan yang lebih efektif berdasarkan data benchmark ini.

# Least Connection
## Script (Mylta - Load Balancer)
```bash
echo ' # Menggunakan Least Connection
 upstream myweb  {
        least_conn;
        server 10.69.4.2; #IP Serverny
        server 10.69.4.3; #IP Stalber
        server 10.69.4.4; #IP Lipovka
 }

 server {
        listen 80;
        server_name _;

        location / {
        proxy_pass http://myweb;
        }
 }' > /etc/nginx/sites-enabled/lb-jarkom

service nginx restart
```

### Benchmark
#### Severny
```
root@Gatka:~# ab -n 1000 -c 100 http://10.69.4.2:8001/
This is ApacheBench, Version 2.3 <$Revision: 1706008 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/
Benchmarking 10.69.4.2 (be patient)
Completed 100 requests
Completed 200 requests
Completed 300 requests
Completed 400 requests
Completed 500 requests
Completed 600 requests
Completed 700 requests
Completed 800 requests
Completed 900 requests
Completed 1000 requests
Finished 1000 requests

Server Software:        nginx/1.10.3
Server Hostname:        10.69.4.2
Server Port:            8001
Document Path:          /
Document Length:        172 bytes
Concurrency Level:      100
Time taken for tests:   0.726 seconds
Complete requests:      1000
Failed requests:        0
Total transferred:      318000 bytes
HTML transferred:       172000 bytes
Requests per second:    1376.74 [#/sec] (mean)
Time per request:       72.635 [ms] (mean)
Time per request:       0.726 [ms] (mean, across all concurrent requests)
Transfer rate:          427.54 [Kbytes/sec] received

Connection Times (ms)
            min mean[+/-sd] median max
Connect:    5 29 9.5 29 58
Processing: 20 42 7.9 43 58
Waiting:    20 42 8.0 43 58
Total:      28 71 9.2 71 87

Percentage of the requests served within a certain time (ms)
50% 71
66% 76
75% 77
80% 78
90% 80
95% 83
98% 85
99% 86
100% 87 (longest request)
```

#### Stalber
```
root@Gatka:~# ab -n 1000 -c 100 http://10.69.4.3:8002/
This is ApacheBench, Version 2.3 <$Revision: 1706008 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/
Benchmarking 10.69.4.3 (be patient)
Completed 100 requests
Completed 200 requests
Completed 300 requests
Completed 400 requests
Completed 500 requests
Completed 600 requests
Completed 700 requests
Completed 800 requests
Completed 900 requests
Completed 1000 requests
Finished 1000 requests

Server Software:        nginx/1.10.3
Server Hostname:        10.69.4.3
Server Port:            8002
Document Path:          /
Document Length:        172 bytes
Concurrency Level:      100
Time taken for tests:   0.697 seconds
Complete requests:      1000
Failed requests:        0
Total transferred:      318000 bytes
HTML transferred:       172000 bytes
Requests per second:    1435.28 [#/sec] (mean)
Time per request:       69.673 [ms] (mean)
Time per request:       0.697 [ms] (mean, across all concurrent requests)
Transfer rate:          445.72 [Kbytes/sec] received

Connection Times (ms)
            min mean[+/-sd] median max
Connect:    4 30 8.4 32 47
Processing: 14 37 6.1 37 52
Waiting:    14 37 6.1 37 51
Total:      19 68 11.5 68 89

Percentage of the requests served within a certain time (ms)
50% 68
66% 75
75% 76
80% 77
90% 80
95% 81
98% 83
99% 84
100% 89 (longest request)
```
Analisis: Stalber menunjukkan sedikit keunggulan dalam hal kecepatan respons dan throughput. Ini mungkin menunjukkan bahwa alokasi sumber daya atau konfigurasi di Stalber sedikit lebih optimal untuk jenis beban yang diberikan selama benchmarking. Ini bisa disebabkan oleh variasi dalam kondisi jaringan pada saat benchmarking.

# IP Hash
## Script (Mylta - Load Balancer)
```bash
echo ' # Menggunakan IP Hash
 upstream myweb  {
        ip_hash;
        server 10.69.4.2; #IP Serverny
        server 10.69.4.3; #IP Stalber
        server 10.69.4.4; #IP Lipovka
 }

 server {
        listen 80;
        server_name _;

        location / {
        proxy_pass http://myweb;
        }
 }' > /etc/nginx/sites-enabled/lb-jarkom

service nginx restart
```

### Benchmark
#### Severny
```
root@Gatka:~# ab -n 1000 -c 100 http://10.69.4.2:8001/
This is ApacheBench, Version 2.3 <$Revision: 1706008 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/
Benchmarking 10.69.4.2 (be patient)
Completed 100 requests
Completed 200 requests
Completed 300 requests
Completed 400 requests
Completed 500 requests
Completed 600 requests
Completed 700 requests
Completed 800 requests
Completed 900 requests
Completed 1000 requests
Finished 1000 requests

Server Software:        nginx/1.10.3
Server Hostname:        10.69.4.2
Server Port:            8001
Document Path:          /
Document Length:        172 bytes
Concurrency Level:      100
Time taken for tests:   0.694 seconds
Complete requests:      1000
Failed requests:        0
Total transferred:      318000 bytes
HTML transferred:       172000 bytes
Requests per second:    1441.89 [#/sec] (mean)
Time per request:       69.353 [ms] (mean)
Time per request:       0.694 [ms] (mean, across all concurrent requests)
Transfer rate:          447.78 [Kbytes/sec] received

Connection Times (ms)
            min mean[+/-sd] median max
Connect:    4 30 7.9 30 46
Processing: 20 37 5.7 39 47
Waiting:    17 37 5.8 38 47
Total:      24 67 10.4 69 80

Percentage of the requests served within a certain time (ms)
50% 69
66% 72
75% 74
80% 75
90% 75
95% 77
98% 78
99% 78
100% 80 (longest request)
```

#### Stalber
```
root@Gatka:~# ab -n 1000 -c 100 http://10.69.4.3:8002/
This is ApacheBench, Version 2.3 <$Revision: 1706008 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/
Benchmarking 10.69.4.3 (be patient)
Completed 100 requests
Completed 200 requests
Completed 300 requests
Completed 400 requests
Completed 500 requests
Completed 600 requests
Completed 700 requests
Completed 800 requests
Completed 900 requests
Completed 1000 requests
Finished 1000 requests

Server Software:        nginx/1.10.3
Server Hostname:        10.69.4.3
Server Port:            8002
Document Path:          /
Document Length:        172 bytes
Concurrency Level:      100
Time taken for tests:   0.686 seconds
Complete requests:      1000
Failed requests:        0
Total transferred:      318000 bytes
HTML transferred:       172000 bytes
Requests per second:    1458.61 [#/sec] (mean)
Time per request:       68.559 [ms] (mean)
Time per request:       0.686 [ms] (mean, across all concurrent requests)
Transfer rate:          452.97 [Kbytes/sec] received

Connection Times (ms)
            min mean[+/-sd] median max
Connect:    6 27 9.4 27 49
Processing: 12 38 14.2 40 251
Waiting:    12 38 14.2 39 251
Total:      18 65 14.7 64 271

Percentage of the requests served within a certain time (ms)
50% 64
66% 68
75% 70
80% 71
90% 76
95% 78
98% 79
99% 80
100% 271 (longest request)
```
Analisis: Stalber unggul dalam beberapa metrik seperti requests per second dan transfer rate, tetapi outlier pada waktu layanan terpanjangnya menunjukkan potensi masalah dalam penanganan kasus-kasus tertentu yang lebih kompleks atau membutuhkan lebih banyak waktu. Severny menunjukkan distribusi waktu respons yang lebih konsisten dan stabil, tanpa adanya outlier yang ekstrem seperti di Stalber.

# Generic Hash
## Script (Mylta - Load Balancer)
```bash
echo ' # Menggunakan Generic Hash
 upstream myweb  {
        hash $request_uri consistent;
        server 10.69.4.2; #IP Serverny
        server 10.69.4.3; #IP Stalber
        server 10.69.4.4; #IP Lipovka
 }

 server {
        listen 80;
        server_name _;

        location / {
        proxy_pass http://myweb;
        }
 }' > /etc/nginx/sites-enabled/lb-jarkom

service nginx restart
```

### Benchmark
#### Severny
```
root@Gatka:~# ab -n 1000 -c 100 http://10.69.4.2:8001/
This is ApacheBench, Version 2.3 <$Revision: 1706008 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/
Benchmarking 10.69.4.2 (be patient)
Completed 100 requests
Completed 200 requests
Completed 300 requests
Completed 400 requests
Completed 500 requests
Completed 600 requests
Completed 700 requests
Completed 800 requests
Completed 900 requests
Completed 1000 requests
Finished 1000 requests

Server Software:        nginx/1.10.3
Server Hostname:        10.69.4.2
Server Port:            8001
Document Path:          /
Document Length:        172 bytes
Concurrency Level:      100
Time taken for tests:   0.701 seconds
Complete requests:      1000
Failed requests:        0
Total transferred:      318000 bytes
HTML transferred:       172000 bytes
Requests per second:    1426.67 [#/sec] (mean)
Time per request:       70.093 [ms] (mean)
Time per request:       0.701 [ms] (mean, across all concurrent requests)
Transfer rate:          443.05 [Kbytes/sec] received

Connection Times (ms)
            min mean[+/-sd] median max
Connect:    3 31 10.0 33 46
Processing: 21 36 5.3 35 58
Waiting:    21 36 5.2 35 58
Total:      26 66 11.2 67 81

Percentage of the requests served within a certain time (ms)
50% 67
66% 71
75% 75
80% 76
90% 78
95% 80
98% 80
99% 80
100% 81 (longest request)
```

#### Stalber
```
root@Gatka:~# ab -n 1000 -c 100 http://10.69.4.3:8002/
This is ApacheBench, Version 2.3 <$Revision: 1706008 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/
Benchmarking 10.69.4.3 (be patient)
Completed 100 requests
Completed 200 requests
Completed 300 requests
Completed 400 requests
Completed 500 requests
Completed 600 requests
Completed 700 requests
Completed 800 requests
Completed 900 requests
Completed 1000 requests
Finished 1000 requests

Server Software:        nginx/1.10.3
Server Hostname:        10.69.4.3
Server Port:            8002
Document Path:          /
Document Length:        172 bytes
Concurrency Level:      100
Time taken for tests:   0.723 seconds
Complete requests:      1000
Failed requests:        0
Total transferred:      318000 bytes
HTML transferred:       172000 bytes
Requests per second:    1382.44 [#/sec] (mean)
Time per request:       72.336 [ms] (mean)
Time per request:       0.723 [ms] (mean, across all concurrent requests)
Transfer rate:          429.31 [Kbytes/sec] received

Connection Times (ms)
            min mean[+/-sd] median max
Connect:    5 33 9.8 34 50
Processing: 12 37 6.9 36 63
Waiting:    12 37 6.8 36 62
Total:      17 70 11.9 69 87

Percentage of the requests served within a certain time (ms)
50% 69
66% 76
75% 80
80% 82
90% 83
95% 85
98% 86
99% 86
100% 87 (longest request)
```
Analisis: Severny memiliki sedikit keunggulan dalam hampir semua metrik yang diukur, termasuk requests per second yang lebih tinggi dan waktu respons yang sedikit lebih cepat. Severny menunjukkan sedikit lebih konsisten dalam waktu respons, dengan waktu penanganan request terlama yang lebih pendek dibandingkan Stalber.

## Kesimpulan
Dari semua hasil benchmark pada berbagai algoritma load balancing (Round Robin, Least Connections, IP Hash, dan Generic Hash), Severny cenderung menunjukkan performa yang lebih baik atau setidaknya unggul dalam beberapa aspek dalam seluruh benchmark. Secara umum, Severny tampak memiliki throughput yang baik, kecepatan respons yang konsisten, dan waktu pemrosesan yang efisien di sebagian besar pengujian. Meskipun ada beberapa aspek di mana Stalber lebih unggul.