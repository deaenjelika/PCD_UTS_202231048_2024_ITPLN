IL (Python Imaging Library): Digunakan untuk mengelola gambar dalam berbagai format. 
Dalam konteks ini, PIL (khususnya submodul Image) digunakan untuk memuat, 
mengonversi, dan memanipulasi gambar dengan mudah, yang penting untuk pekerjaan praproses dalam analisis gambar.
• NumPy: Sebuah library yang menyediakan dukungan untuk array dan matriks besar serta 
berbagai fungsi matematika tingkat tinggi. NumPy digunakan di sini untuk manipulasi 
efisien pada data gambar yang diwakili sebagai array, memungkinkan operasi yang 
kompleks dan cepat pada data gambar.
• Matplotlib: Library yang sering digunakan untuk membuat visualisasi data dalam Python. 
Matplotlib digunakan untuk menampilkan gambar dan hasil modifikasi agar mudah 
dianalisis secara visual.
Image.open dan convert: Mengonversi gambar ke format RGB untuk memastikan 
konsistensi dalam representasi warna, karena beberapa gambar mungkin tidak dalam format 
RGB. Ini penting untuk analisis warna berikutnya, di mana kita perlu memisahkan setiap saluran warna.
np.array: Transformasi gambar PIL ke array NumPy memfasilitasi manipulasi data secara 
numerik, karena NumPy dirancang untuk operasi efisien pada array besar, yang sangat 
berguna dalam pengolahan gambar.

1. Membuat Salinan Gambar Asli
Kode ini memulai dengan membuat tiga salinan dari gambar asli yang diwakili oleh 
img_array. Salinan ini dibuat agar manipulasi yang dilakukan pada gambar tidak mengubah 
gambar asli dan memungkinkan manipulasi independen pada masing-masing versi gambar
highlighted_dea = img_array.copy()
highlighted_enj = img_array.copy()
highlighted_eli = img_array.copy()
Setiap salinan akan digunakan untuk menonjolkan salah satu saluran warna—merah untuk 
DEA, biru untuk ENJ, dan hijau untuk ELI.

3. Menetapkan Warna Berdasarkan Masker
Untuk setiap salinan gambar, dilakukan proses pengeditan piksel berdasarkan masker yang 
telah ditentukan (r_mask untuk merah, b_mask untuk biru, g_mask untuk hijau). Ini 
dilakukan dengan dua langkah:
a. Menyoroti Area dengan Warna Tertentu
highlighted_dea[r_mask] = [128, 128, 128]: Piksel yang memenuhi r_mask (area dimana 
intensitas merah melebihi threshold tertentu) diatur menjadi abu-abu (128, 128, 128). Ini 
menonjolkan area dimana warna merah adalah dominan.
highlighted_enj[b_mask] = [128, 128, 128]: Serupa dengan DEA, namun untuk biru. Area 
yang memenuhi b_mask ditampilkan dalam abu-abu.
highlighted_eli[g_mask] = [128, 128, 128]: Untuk hijau, area yang memenuhi g_mask 
diubah menjadi abu-abu.

b. Menghitamkan Area Lain
highlighted_dea[~r_mask] = [0, 0, 0]: Piksel yang tidak memenuhi r_mask diatur menjadi 
hitam, yang berarti semua area selain yang merah dominan akan menjadi hitam.
highlighted_enj[~b_mask] = [0, 0, 0]: Sama seperti DEA, namun untuk masker biru. Area 
yang tidak biru menjadi hitam.
highlighted_eli[~g_mask] = [0, 0, 0]: Untuk hijau, piksel yang tidak memenuhi masker hijau 
dihitamkan.
• Image.fromarray(): Fungsi ini digunakan untuk mengonversi array yang telah dimodifikasi 
kembali menjadi format gambar PIL untuk visualisasi
fig, ax = plt.subplots(1, 4, figsize=(24, 6)): Membuat figur dan subplot untuk menampilkan 
gambar asli dan gambar yang telah dimodifikasi.
• ax[0].imshow(img_rgb) dan seterusnya: Menampilkan gambar asli dan gambar yang 
dimodifikasi dalam subplot yang berbeda.
• ax[0].set_title("Original Image") dan seterusnya: Menetapkan judul untuk setiap subplot.
• ax[0].axis('off') dan seterusnya: Menonaktifkan sumbu (axis) pada setiap subplot untuk 
kejelasan visual
1. none_mask = ~(r_mask | g_mask | b_mask)
Deskripsi: Membuat masker untuk area yang tidak memiliki intensitas warna R, G, atau B di 
atas threshold tertentu
Logika: ~(r_mask | g_mask | b_mask) berarti masker ini adalah kebalikan dari gabungan masker 
R, G, dan B. Ini artinya, none_mask akan menjadi True hanya pada lokasi di mana semua 
saluran R, G, dan B berada di bawah threshold yang ditentukan.
Kegunaan: Masker ini biasanya digunakan untuk mengisolasi area yang gelap atau sangat dekat 
dengan hitam di gambar, di mana tidak ada warna dominan yang melebihi ambang batas.

3. blue_mask = b_mask & ~(r_mask | g_mask)
Deskripsi: Membuat masker untuk area di mana warna biru adalah warna dominan.
Logika: b_mask & ~(r_mask | g_mask) menghasilkan masker di mana hanya saluran biru yang 
melebihi threshold dan saluran merah serta hijau berada di bawah threshold.
Kegunaan: Masker ini digunakan untuk menemukan area yang dominan biru, tanpa campuran 
yang signifikan dari warna merah atau hijau.

5. red_blue_mask = r_mask & b_mask & ~g_mask
Deskripsi: Membuat masker untuk area di mana warna merah dan biru adalah dominan tetapi 
tidak hijau.
Logika: r_mask & b_mask & ~g_mask memilih area di mana saluran merah dan biru keduanya 
melebihi threshold, tetapi saluran hijau tidak.
Kegunaan: Ini berguna untuk menyoroti area dengan kombinasi warna magenta di mana merah 
dan biru mendominasi tetapi tanpa kehijauan.

7. red_green_blue_mask = r_mask & g_mask & b_mask
Deskripsi: Membuat masker untuk area di mana ketiga warna, merah, hijau, dan biru, semuanya 
adalah dominan.
Logika: r_mask & g_mask & b_mask menandai hanya area di mana ketiga saluran warna 
melebihi threshold.
Kegunaan: Masker ini digunakan untuk menemukan area yang sangat terang atau putih, di mana 
ketiga saluran warna sama-sama kuat.
Masing-masing masker ini sangat berguna dalam pemrosesan gambar untuk tujuan seperti 
segmentasi gambar, deteksi objek berdasarkan warna, atau manipulasi gambar berdasarkan 
karakteristik warna tertentu. Masker yang dihasilkan ini dapat digunakan secara langsung untuk 
memodifikasi gambar atau untuk analisis lebih lanjut.
1. Copy Gambar Original
Untuk setiap masker yang berbeda (none, blue, red-blue, red-green-blue), gambar asli 
disalin. Ini dilakukan untuk memastikan bahwa modifikasi yang dibuat pada gambar tidak 
mengubah gambar asli, sehingga memungkinkan operasi independen pada setiap salinan 
tanpa mempengaruhi gambar yang lain.
img_array.copy(): Metode ini membuat salinan independen dari array img_array, yang 
mewakili gambar asli. Salinan ini digunakan sebagai dasar untuk modifikasi lebih lanjut.
2. Aplikasi Masker dan Penyesuaian Warna
Setiap baris berikutnya menggunakan masker boolean yang telah dibuat untuk memodifikasi 
gambar terkait
a. highlighted_none
highlighted_none[~none_mask] = [0, 0, 0]:
Masker (~none_mask): Menggunakan negasi dari none_mask. Di sini, ~none_mask menjadi 
True di lokasi di mana none_mask adalah False, yang berarti menargetkan area di mana 
semua saluran warna berada di bawah threshold (disebut sebagai "tidak ada").
Pengaturan Warna: Menetapkan piksel di lokasi tersebut menjadi hitam ([0, 0, 0]), efektif 
menghilangkan semua warna di area tersebut, sehingga hanya menonjolkan area yang tidak 
masuk dalam kategori "none".
b. highlighted_blue
highlighted_blue[~blue_mask] = [0, 0, 0]:
Masker (~blue_mask): Memilih area di luar masker biru.
Pengaturan Warna: Menjadikan area luar masker biru menjadi hitam, menonjolkan hanya 
area biru yang dominan dalam gambar.
c. highlighted_red_blue
highlighted_red_blue[~red_blue_mask] = [0, 0, 0]
Masker (~red_blue_mask): Memilih area di luar kombinasi masker merah dan biru.
Pengaturan Warna: Mengubah area di luar kombinasi warna merah dan biru menjadi hitam, 
memungkinkan fokus pada area dengan campuran warna merah dan biru saja.
d. highlighted_red_green_blue
highlighted_red_green_blue[~red_green_blue_mask] = [0, 0, 0]:
Masker (~red_green_blue_mask): Menargetkan area di luar masker di mana semua tiga warna dominan.
Pengaturan Warna: Area di luar masker tiga warna menjadi hitam, menonjolkan hanya area 
yang sangat terang atau putih di gambar.
Tujuan dari modifikasi ini adalah untuk visualisasi yang jelas dan mudah dikenali dari ciriciri warna tertentu dalam gambar.
Ini bisa sangat berguna dalam aplikasi seperti analisis 
gambar, pengolahan citra medis, atau sistem pengenalan pola, di mana pemahaman visual mengenai distribusi dan dominasi warna dapat memberikan informasi penting
Menampilkan hasil ouput nilai ambang batas berdasarkan segmentasi warna pada masker objek


