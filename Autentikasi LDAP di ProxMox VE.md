# Autentikasi LDAP di ProxMox VE

Panduan ini menjelaskan cara mengkonfigurasi autentikasi LDAP untuk ProxMox VE agar pengguna dapat login menggunakan akun dari Active Directory.

## Prasyarat
- Server ProxMox VE yang sudah berjalan.
- Active Directory (AD) dengan pengguna dan grup yang telah dibuat.
- Hak akses administratif di ProxMox VE dan AD.

## 1. Membuat Grup VMAdmins di Active Directory
1. Buka **Active Directory Users and Computers** di Windows.
2. Perluas domain Anda.
3. Klik kanan pada **Users** > **New** > **Group**.
4. Beri nama grup **VMAdmins** > Klik **OK**.
5. Klik dua kali grup **VMAdmins**, pilih tab **Members**, lalu klik **Add...**.
6. Tambahkan pengguna yang akan menjadi administrator ProxMox.

## 2. Konfigurasi LDAP di ProxMox VE
1. Buka browser dan akses UI web ProxMox VE.
2. Login sebagai administrator.
3. Pilih **Datacenter** di menu navigasi kiri.
4. Pilih **Permissions** > **Authentication**.
5. Klik **Add** > **LDAP Server**.
6. Isi formulir LDAP sebagai berikut:
   - **Realm**: `i12bretro.local`
   - **Base Domain Name**: `CN=Users,DC=i12bretro,DC=local`
   - **Server**: `10.10.27.1`
   - **User Attribute Name**: `sAMAccountName`
7. Klik **Sync Options**, lalu isi formulir dengan:
   - **Bind User**: `CN=readonly_svc,CN=Users,DC=i12bretro,DC=local`
   - **Bind Password**: `Read0nly!!`
   - **E-Mail Attribute**: `mail`
   - **User Classes**: `person, user`
   - **Group Classes**: `group`
   - **User Filter**: `(&(memberOf=CN=VMAdmins,CN=Users,DC=i12bretro,DC=local))`
   - **Group Filter**: `(&(distinguishedName=CN=VMAdmins,CN=Users,DC=i12bretro,DC=local))`
   - **Scope**: `Users and Groups`
   - **Enable new users**: `Yes`
   - **Full**: `Yes`
   - **Purge**: `Yes`
8. Klik **Add** untuk menyimpan konfigurasi.

## 3. Sinkronisasi LDAP
1. Kembali ke halaman **Authentication**.
2. Pilih metode autentikasi LDAP yang baru dibuat dan klik **Sync**.
3. Klik **Preview** untuk melihat hasil sinkronisasi sebelum menyimpannya.
4. Jika sudah sesuai, jalankan sinkronisasi ulang untuk membuat pengguna dan grup di ProxMox.

## 4. Mengatur Izin Grup
1. Pilih **Permissions** di menu navigasi kiri.
2. Klik **Add** > **Group Permission**.
3. Atur path ke `/`, pilih grup **VMAdmins**, dan pilih **Role** yang diinginkan.
4. Klik **Add**.

## 5. Pengujian Login
1. Logout dari UI ProxMox.
2. Login kembali menggunakan salah satu akun dari grup **VMAdmins**.
3. Pastikan **Realm** diatur ke domain yang dibuat sebelumnya.

---
### Catatan
- Pastikan koneksi antara ProxMox VE dan Active Directory berjalan dengan baik.
- Jika terjadi kesalahan saat sinkronisasi, periksa konfigurasi LDAP dan pastikan kredensial yang digunakan benar.

---
**Dokumentasi ini dibuat untuk membantu dalam konfigurasi autentikasi LDAP di ProxMox VE.**
