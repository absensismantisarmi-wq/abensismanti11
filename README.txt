ABSENSI FACE ID SMA NEGERI 3 SARMI — MODE AKUN KIOS BERSAMA

Isi paket:
- index.html: pilihan Admin/Pengguna
- admin.html: halaman admin dari paket sebelumnya
- pengguna.html: login satu akun kios, pilih nama, PIN pribadi, verifikasi wajah, lalu absensi
- supabase-shared-kiosk.sql: fungsi RPC dan RLS untuk kios

PEMASANGAN
1. Cadangkan file di GitHub terlebih dahulu. Unggah index.html, admin.html, pengguna.html ke root repository.
2. Periksa struktur tabel Supabase sebelum menjalankan SQL. SQL mengasumsikan employees.id dan attendance.employee_id bertipe text, employees memiliki name, employee_type, active, consent, face_descriptor, dan attendance memiliki id, employee_id, work_date, check_in, check_out, status.
3. Jalankan supabase-shared-kiosk.sql di SQL Editor setelah membaca seluruh SQL. SQL ini mengganti policies RLS pada employees, attendance, user_profiles; periksa policy lama sebelum menjalankan. RLS tidak dimatikan.
4. Buat akun Auth khusus kios. Tambahkan profil role kiosk dengan UUID akun: insert into public.user_profiles(user_id,role,employee_id) values ('UUID-AKUN-KIOS','kiosk',null);
5. Tetapkan PIN unik untuk setiap pegawai sebagai hash di database. Contoh perintah (gunakan PIN berbeda, jangan gunakan contoh PIN): update public.employees set pin_hash=crypt('PIN_BARU',gen_salt('bf')) where id='ID_PEGAWAI'; Jalankan satu per pegawai lewat SQL Editor; jangan simpan PIN di GitHub.
6. Admin mendaftarkan template wajah dan memastikan consent=true. Uji dengan akun kios dan beberapa pegawai.

BATASAN PENTING
- Akun login bersama membuat jejak Auth menunjukkan akun kios, bukan identitas personal; PIN membantu mengaitkan tindakan ke pegawai tetapi bukan MFA.
- Verifikasi wajah dilakukan di browser. Parameter p_face_match berasal dari browser dan bisa dimanipulasi oleh pengguna ahli; ini bukan verifikasi biometrik server-side dan tidak memiliki liveness detection. Jangan menganggapnya bukti anti-penipuan atau gunakan sebagai satu-satunya dasar catatan resmi sebelum audit keamanan.
- Fungsi kiosk_get_face_template mengembalikan template hanya setelah PIN yang benar, tetapi browser tetap menerima template pegawai yang dipilih. Jaga PIN, batasi akses fisik ke perangkat kios, dan sediakan metode absensi alternatif.
- Jangan pernah memasukkan service_role/secret key ke HTML atau GitHub. Gunakan HTTPS dan publishable/anon key saja.
- SQL harus disesuaikan jika nama/tipe kolom berbeda. Periksa terlebih dahulu, jangan jalankan membabi buta pada database produksi.
