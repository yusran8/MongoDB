# Implementasi MongoDB

## Spesifikasi server, config, dan router
- config-1     : 192.168.17.105 memory 512MB
- config-2     : 192.168.17.106 memory 512MB
- query-router : 192.168.17.107 memory 512MB
- shard-1      : 192.168.17.108 memory 512MB
- shard-2      : 192.168.17.109 memory 512MB
- shard-3      : 192.168.17.110 memory 512MB

## Implementasi
1. Pastikan Vagrant sudah terinstall pada komputer.
2. Pada direktori yang diinginkan, buat sebuah file "vagrantFile" dengan menjalankan perintah `vagrant init`
3. Edit "Vagrantfile" menjadi seperti "Vagrantfile" yang terlampir di github ini.
4. Untuk provisioning, buat sebuah file dalam folder "provision" dan edit menjadi seperti file "allhost.sh"
5. Masih dalam direktori yang sama, jalankan perintah `vagrant up`

### Konfigurasi config node

1. Pada salah satu config node, masuk ke mongo shell dan buat sebuah user admin dengan priviliege root 
   ```
   mongo
   use admin
   db.createUser({user: "mongo-admin", pwd: "password", roles:[{role: "root", db: "admin"}]})
   ```
2. Pada setiap node config, buka file "/etc/mongod.conf"
   - ubah bindIp sesuai ip masing-masing node
   - tambahkan
     ```
     replication:
     replSetName: configReplSet
     ```
   - tambahkan juga 
     ```
     sharding:
     clusterRole: "configsvr"
     ```
   - restart mongod menggunakan command `sudo systemctl restart mongod`
3. Pada salah satu config node, masuk ke mongoDB shell 
   `mongo 192.168.17.105:27019 -u mongo-admin -p --authenticationDatabase admin`
   dengan password = "password"
4. inisialisasi replika set 
   ```rs.initiate( { _id: "configReplSet", configsvr: true, members: [ { _id: 0, host: "192.168.17.105:27019" }, { _id: 1, host:          "192.168.17.106:27019" } ] } )
   ```
   - jika berhasil akan muncul pesan `{ "ok" : 1 }`

### Konfigurasi query router

1. Buat sebuah file "/etc/mongos.conf", dan isikan seperti pada di bawah:
   ```
   # where to write logging data
    systemLog:
      destination: file
      logAppend: true
      path: /var/log/mongodb/mongos.log

    # network interfaces
    net:
      port: 27017
      bindIp: 192.168.17.107

    sharding:
      configDB: configReplSet/192.168.17.105:27019,192.168.17.106:27019    
     ```
2. Buat sebuah fule systemd unit untuk mongo `/lib/systemd/system/mongos.service` dengan isi:
   ```
    [Unit]
    Description=Mongo Cluster Router
    After=network.target

    [Service]
    User=mongodb
    Group=mongodb
    ExecStart=/usr/bin/mongos --config /etc/mongos.conf
    # file size
    LimitFSIZE=infinity
    # cpu time
    LimitCPU=infinity
    # virtual memory size
    LimitAS=infinity
    # open files
    LimitNOFILE=64000
    # processes/threads
    LimitNPROC=64000
    # total threads (user+kernel)
    TasksMax=infinity
    TasksAccounting=false

    [Install]
    WantedBy=multi-user.target
   ```
 
3. Pastikan mongod dalam keadaan stop `sudo systemctl stop mongod`
4. Enable mongos.service agar berjalan otomatis saat reboot
   ```
   sudo systemctl enable mongos.service
   sudo systemctl start mongos 
   ```
5. Pastikan mongos berjalan `systemctl status mongos`

### Mengatur Shard node

1. Masuk ke setiap shard server dan buka "/etc/mongod.conf"
   - sesuaikan ip masing-masing
   - tambahkan 
     ```
      sharding:
     clusterRole: "shardsvr"
     ```
   - restart shard `sudo service mongod restart`
2. Pada salah satu shard server connect ke query route `mongo 192.168.17.107:27017 -u mongo-admin -p --authenticationDatabase admin` dengan password = "pasword"
3. tambahkan setiap shard dari mongo interface
   ```
   sh.addShard( "192.168.17.108:27017" )
   sh.addShard( "192.168.17.109:27017" )
   sh.addShard( "192.168.17.110:27017" )
   ```

### Sharding database
1. masuk ke qouery router `mongo 192.168.17.107:27017 -u mongo-admin -p --authenticationDatabase admin`
2. gunakan database yang diinginkan `use books`
3. enable sharding dengan perintah `sh.enableSharding("books")`
4. buat sebuah collection baru dengan id yang di hash `db.books.ensureIndex( { _id : "hashed" } )`
5. sharding collection dengan perintah `sh.shardCollection( "books.books", { "_id" : "hashed" } )`

### Testing 
1. import dataset `mongoimport --host=192.168.17.107 -d books -c books --type csv --file books3.csv --headerline`
2. masuk ke query router, gunakan db yang diimport tadi kemudian ketikkan perintah `db.books.getShardDistribution()`

