# Tugas Praktikum 3
<img src=https://qph.fs.quoracdn.net/main-qimg-648763cc041459725b62108f4fdf5b91 width="110px" >

| **Nama**              | **NIM**   | **Kelas** | **Matkul** |
| --------------------- | --------- | --------- | ---------- |
| Dhiyaulhaq Al Maududi | 312310508 | TI.23.A5  | Basis Data |

## Script DDL berdasarkan skema ERD
```sql
CREATE DATABASE praktikum3;
USE praktikum3;

CREATE TABLE Mahasiswa (
  nim VARCHAR(10) PRIMARY KEY,
  nama VARCHAR(50) NOT NULL,
  jenis_kelamin ENUM('Laki-laki', 'Perempuan') NOT NULL,
  tgl_lahir DATE NOT NULL,
  jalan VARCHAR(100) DEFAULT NULL,
  kota VARCHAR(50) NOT NULL,
  kodepos VARCHAR(10) DEFAULT NULL,
  no_hp VARCHAR(20) DEFAULT NULL,
  kd_ds VARCHAR(10) NOT NULL,
  CONSTRAINT FK_DosenWali FOREIGN KEY (kd_ds) REFERENCES Dosen(kd_ds)
);

CREATE TABLE Dosen (
  kd_ds VARCHAR(10) PRIMARY KEY,
  nama VARCHAR(50) NOT NULL
);

CREATE TABLE MataKuliah (
  kd_mk VARCHAR(10) PRIMARY KEY,
  nama VARCHAR(50) NOT NULL,
  sks INT NOT NULL
);

CREATE TABLE JadwalMengajar (
  kd_ds VARCHAR(10) NOT NULL,
  kd_mk VARCHAR(10) NOT NULL,
  hari ENUM('Senin', 'Selasa', 'Rabu', 'Kamis', 'Jumat', 'Sabtu', 'Minggu') NOT NULL,
  jam TIME NOT NULL,
  ruang VARCHAR(20) NOT NULL,
  PRIMARY KEY (kd_ds, kd_mk, hari, jam),
  CONSTRAINT FK_JadwalDosen FOREIGN KEY (kd_ds) REFERENCES Dosen(kd_ds),
  CONSTRAINT FK_JadwalMatakuliah FOREIGN KEY (kd_mk) REFERENCES MataKuliah(kd_mk)
);

CREATE TABLE KRSMahasiswa (
  nim VARCHAR(10) NOT NULL,
  kd_mk VARCHAR(10) NOT NULL,
  kd_ds VARCHAR(10) NOT NULL,
  semester VARCHAR(10) NOT NULL,
  nilai DECIMAL(4,2) NOT NULL,
  PRIMARY KEY (nim, kd_mk),
  CONSTRAINT FK_KRSMahasiswa FOREIGN KEY (nim) REFERENCES Mahasiswa(nim),
  CONSTRAINT FK_KRSMataKuliah FOREIGN KEY (kd_mk) REFERENCES MataKuliah(kd_mk),
  CONSTRAINT FK_KRSDosen FOREIGN KEY (kd_ds) REFERENCES Dosen(kd_ds)
);
```

### Tugas Praktikum 3

#### 1. Lakukan penambahan data pada table mahasiswa dengan mengisi *kd_ds* yang belum ada pada data dosen.
**Script :**
```sql
INSERT INTO Dosen (kd_ds, nama) VALUES
('DS001', 'uden'),
('DS002', 'kamul'),
('DS003', 'udin'),
('DS004', 'amar'),
('DS005', 'pekir');
select * from Dosen;

![Screenshot 2024-05-20 123851](https://github.com/Pynixz/Praktikum3.S2/assets/147568964/52bc950f-c379-415c-ad31-a09f7ac10ba8)


INSERT INTO Mahasiswa (nim, nama, jenis_kelamin, tgl_lahir, jalan, kota, kodepos, no_hp, kd_ds) VALUES
('11223344', 'uden', 'Perempuan', '2005-07-14', NULL, 'Cikarang', NULL, NULL, 'DS001'),
('11223345', 'kamul', 'Laki-laki', '2005-11-16', NULL, 'Cikarang', NULL, NULL, 'DS002'),
('11223347', 'udin', 'Perempuan', '2002-01-02', NULL, 'Bekasi', NULL, NULL, 'DS003'),
('11223348', 'amar', 'Laki-laki', '2005-02-05', NULL, 'Bekasi', NULL, NULL, 'DS004'),
('11223349', 'pekir', 'Laki-laki', '2005-03-10', NULL, 'Cikarang', NULL, NULL, 'DS005');
select * from Mahasiswa;
```

#### 2. Hapus satu record data pada table dosen yang telah dirujuk pada tabel mahasiswa.
```sql
DELETE FROM Dosen WHERE kd_ds = 'DS005';
```
![Screenshot 2024-05-20 123926](https://github.com/Pynixz/Praktikum3.S2/assets/147568964/16433347-e69f-4ced-8822-be9119e54d65)


**Keterangan :** Terjadi ERROR dikarenakan `kd_ds` pada tabel Mahasiswa merupakan **FOREIGN KEY** dari tabel refensinya yaitu tabel Dosen. Dan pada tabel Dosen `kd_ds` merupakan **PRIMARY KEY**. Itu artinya, tabel Dosen sebagai tabel *parent/references* dan Mahasiswa sebagai tabel *child* maka dari itu saat menghapus satu record data pada tabel dosen terjadi error. 

#### 3. Ubah mode menjadi *ON UPDATE CASCADE ON DELETE RESTRICT*
```sql
ALTER TABLE Mahasiswa DROP FOREIGN KEY FK_DosenWali;
ALTER TABLE Mahasiswa ADD CONSTRAINT FK_DosenMahasiswa FOREIGN KEY (kd_ds) REFERENCES Dosen(kd_ds) ON UPDATE CASCADE ON DELETE RESTRICT;
```
![Screenshot 2024-05-20 124857](https://github.com/Pynixz/Praktikum3.S2/assets/147568964/7e90bed2-5939-4da0-abcd-c9a8846b648c)


#### 4. Lakukan perubahan data pada table dosen *(kd_ds)*
```sql
UPDATE Dosen SET kd_ds = WHERE kd_ds = 'DS005' WHERE nama = 'amar';
```
![Screenshot 2024-05-20 124356](https://github.com/Pynixz/Praktikum3.S2/assets/147568964/c04eee43-6d9e-4929-b731-ef611ba46f20)


**Keterangan :** `kd_ds` dapat diubah dikarenakan sebelumnya menggunakan `ON UPDATE CASCADE`

#### 5. Lakukan penghapusan data pada table dosen
```sql
DELETE FROM Dosen WHERE kd_ds = 'DS003';
```
![Screenshot 2024-05-20 124924](https://github.com/Pynixz/Praktikum3.S2/assets/147568964/83b57c4e-bf0d-4ad9-ad00-447608025a9c)


**Keterangan :** Terjadi ERROR dan `kd_ds` tidak dapat dihapus.

#### 6. Ubah mode menjadi *ON UPDATE CASCADE ON DELETE SET NULL*
```sql
ALTER TABLE Mahasiswa DROP FOREIGN KEY FK_DosenMahasiswa;
ALTER TABLE Mahasiswa ADD CONSTRAINT FK_DosenWali FOREIGN KEY (kd_ds) REFERENCES Dosen(kd_ds) ON UPDATE CASCADE ON DELETE SET NULL;
```

#### 7. Lakukan penghapusan data pada table dosen
```sql
DELETE FROM Dosen WHERE kd_ds = 'DS004';
```

**Keterangan :** `kd_ds` berhasil dihapus.

 ## Evaluasi dan Pertanyaan
  * Tulis semua perintah-perintah SQL percobaan di atas beserta outputnya!

  ### Syntax SQL

  - Membuat foreign key

    - Dalam ALTER TABLE:
      ```SQL
      ALTER TABLE mahasiswa
      ADD CONSTRAINT fk_dosenwali FOREIGN KEY (kd_ds)     REFERENCES dosen(kd_ds)
      ```
    - Dalam CREATE TABLE:
      ```sql
      CREATE TABLE mahasiswa(
      nim VARCHAR(10) NOT NULL,
      nama VARCHAR(100) NOT NULL,
      kd_ds VARCHAR(10),
      PRIMARY KEY(nim),
      CONSTRAINT fk_DosenWali FOREIGN KEY (kd_ds)
      REFERENCES dosen(kd_ds)
      );
      ```

    ```

    ```

  - Mengubah data
    ```sql
    UPDATE mahasiswa
    SET kd_ds = 'DS011' WHERE nim = 112233445;
    ```
  - Menampilkan CREATE TABLE
    ```sql
    SHOW CREATE TABLE  mahasiswa;
    ```
  - Mode ON UPDATE CASCADE ON DELETE CASCADE
    ```sql
    ALTER TABLE mahasiswa
    DROP FOREIGN KEY fk_mahasiswa_dosen,
    ADD CONSTRAINT fk_dosenwali FOREIGN KEY (kd_ds) REFERENCES dosen(kd_ds) ON UPDATE CASCADE ON DELETE CASCADE;
    ```
  - Menghapus data
    ```sql
    DELETE FROM dosen WHERE kd_ds = 'DS001';
    ```
  - Mode ON UPDATE CASCADE ON DELETE NOT NULL
    ```sql
    ALTER TABLE <table>
    DROP FOREIGN KEY <nama_constraint_lama>,
    ADD CONSTRAINT <nama_constraint_baru> FOREIGN KEY (field) REFERENCES <table_references(filed_references)> ON UPDATE CASCADE ON DELETE NOT NULL;
    ```
  - Mengubah data
    ```sql
    UPDATE dosen
    SET kd_ds = 'DS006' WHERE nama = 'Haha Hihi';
    ```
  - Menghapus data
    ```sql
    DELETE FROM dosen WHERE nim = 'DS003';
      ```

#### Apa bedanya penggunaan RESTRICT dan penggunaan CASCADE
* **Restrict** = yaitu perubahan data dan penghapusan data tidak diijinkan pada tabel referensi (parent table) apabila pada tabel child sudah ada yang merujuk pada data tersebut.
* **Cascade** = yaitu perubahan atau penghapusan data pada tabel referensi (parent table) akan diikuti oleh tabel child.

Keduanya dapat ditambahkan pada action `ON UPDATE` dan `ON DELETE` selain itu, bisa juga digunakan action `SET NULL`.

#### Berikan kesimpulan anda!
Dalam kesimpulannya, **RESTRICT** dan **CASCADE** digunakan untuk mengatur perilaku ketika ada perubahan atau penghapusan data pada tabel utama. Jika digunakan dengan benar, ini dapat membantu memastikan integritas referensial dan konsistensi data antara tabel-tabel yang saling berhubungan.
