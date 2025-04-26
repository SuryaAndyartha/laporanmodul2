# Trabowo & Peddy Movie Night

Trabowo dan sahabatnya, Peddy, sedang menikmati malam minggu di rumah sambil mencari film seru untuk ditonton. Mereka menemukan sebuah file ZIP yang berisi gambar-gambar poster film yang sangat menarik. File tersebut dapat diunduh dari **[Google Drive](https://drive.google.com/file/d/1nP5kjCi9ReDk5ILgnM7UCnrQwFH67Z9B/view?usp=sharing)**. Karena penasaran dengan film-film tersebut, mereka memutuskan untuk membuat sistem otomatis guna mengelola semua file tersebut secara terstruktur dan efisien. Berikut adalah tugas yang harus dikerjakan untuk mewujudkan sistem tersebut:

### **a. Ekstraksi File ZIP**

Trabowo langsung mendownload file ZIP tersebut dan menyimpannya di penyimpanan lokal komputernya. Namun, karena file tersebut dalam bentuk ZIP, Trabowo perlu melakukan **unzip** agar dapat melihat daftar film-film seru yang ada di dalamnya.

### **b. Pemilihan Film Secara Acak**

Setelah berhasil melakukan unzip, Trabowo iseng melakukan pemilihan secara acak/random pada gambar-gambar film tersebut untuk menentukan film pertama yang akan dia tonton malam ini.

**Format Output:**

```
Film for Trabowo & Peddy: ‘<no_namafilm_genre.jpg>’
```

### **c. Memilah Film Berdasarkan Genre**

Karena Trabowo sangat perfeksionis dan ingin semuanya tertata rapi, dia memutuskan untuk mengorganisir film-film tersebut berdasarkan genre. Dia membuat 3 direktori utama di dalam folder `~/film`, yaitu:

- **FilmHorror**
- **FilmAnimasi**
- **FilmDrama**

Setelah itu, dia mulai memindahkan gambar-gambar film ke dalam folder yang sesuai dengan genrenya. Tetapi Trabowo terlalu tua untuk melakukannya sendiri, sehingga ia meminta bantuan Peddy untuk memindahkannya. Mereka membagi tugas secara efisien dengan mengerjakannya secara bersamaan (overlapping) dan membaginya sama banyak. Trabowo akan mengerjakan dari awal, sementara Peddy dari akhir. Misalnya, jika ada 10 gambar, Trabowo akan memulai dari gambar pertama, gambar kedua, dst dan Peddy akan memulai dari gambar kesepuluh, gambar kesembilan, dst. Lalu buatlah file “recap.txt” yang menyimpan log setiap kali mereka selesai melakukan task

Contoh format log :

```
[15-04-2025 13:44:59] Peddy: 50_toystory_animasi.jpg telah dipindahkan ke FilmAnimasi
```

Setelah memindahkan semua film, Trabowo dan Peddy juga perlu menghitung jumlah film dalam setiap kategori dan menuliskannya dalam file **`total.txt`**. Format dari file tersebut adalah:

```
Jumlah film horror: <jumlahfilm>
Jumlah film animasi: <jumlahfilm>
Jumlah film drama: <jumlahfilm>
Genre dengan jumlah film terbanyak: <namagenre>
```

### **d. Pengarsipan Film**

Setelah semua film tertata dengan rapi dan dikelompokkan dalam direktori masing-masing berdasarkan genre, Trabowo ingin mengarsipkan ketiga direktori tersebut ke dalam format **ZIP** agar tidak memakan terlalu banyak ruang di komputernya.

---

### Penyelesaian

a. trabowo-a.c; Code Lengkap:

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>
#include <stdlib.h>

int main(){
    char *zip_file = "/home/ubuntu/sisop_modul_2/resources/film.zip";
    char *unzip_dir = "/home/ubuntu/sisop_modul_2/resources";
    char *check_dir = "/home/ubuntu/sisop_modul_2/resources/film";
    char *download_link = "https://drive.google.com/uc?export=download&id=1nP5kjCi9ReDk5ILgnM7UCnrQwFH67Z9B";
    
    if(access(check_dir, F_OK) == 0){
        printf("Folder unzip sudah ada.\n");
        return 0;
    }

    if(access(zip_file, F_OK) != 0){
        pid_t pid = fork();

        if(pid == 0){
            execlp("wget", "wget", download_link, "-O", zip_file, NULL);
            perror("execlp gagal.\n");
            exit(1);
        }
        else if(pid > 0){
            int flag;
            waitpid(pid, &flag, 0);
            
            if(WIFEXITED(flag) && WEXITSTATUS(flag) == 0){
                printf("Download berhasil.\n");
            }
            else{
                printf("Download gagal.\n");
                return 1;
            }
        }
        else{
            perror("fork gagal.\n");
            return 2;
        }
    }

    pid_t pid = fork();

    if(pid == 0){
        execlp("unzip", "unzip", zip_file, "-d", unzip_dir, NULL);
        perror("execlp gagal.\n");
        exit(1);
    }
    else if(pid > 0){
        int flag;
        waitpid(pid, &flag, 0);

        if(WIFEXITED(flag) && WEXITSTATUS(flag) == 0){
            printf("Unzip berhasil.\n");
        }
        else {
            printf("Unzip gagal.\n");
            return 3;
        }
    }
    else{
        perror("fork gagal.\n");
        return 4;
    }

    return 0;
}
```

Penjelasan:

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>
#include <stdlib.h>
```
Kode dimulai oleh beberapa _library_ penting untuk menjalankan program sesuai yang diinginkan. `#include <stdio.h>` menyediakan akses ke fungsi `printf`. `#include <unistd.h>` memberikan akses ke fungsi `fork`, `execlp`, dan `access`. `#include <sys/wait.h>` menyediakan fungsi `waitpid`, `WIFEXITED`, dan `WEXITSTATUS`. `#include <stdlib.h>` menyediakan fungsi `exit` dan `perror`.

```c
int main(){
    char *zip_file = "/home/ubuntu/sisop_modul_2/resources/film.zip";
    char *unzip_dir = "/home/ubuntu/sisop_modul_2/resources";
    char *check_dir = "/home/ubuntu/sisop_modul_2/resources/film";
    char *download_link = "https://drive.google.com/uc?export=download&id=1nP5kjCi9ReDk5ILgnM7UCnrQwFH67Z9B";
    ...
}
```
`int main(){...}` adalah fungsi utama yang menjadi titik masuk eksekusi program. Di dalamnya, program dimulai dan dieksekusi.
Setelahnya, terdapat bagian yang mendeklarasikan variabel-variabel sebagai _pointer_ dari tipe data `char`. `zip_file` menyimpan _path_ lengkap ke _file ZIP_ yang akan diunduh, yaitu `film.zip`. `unzip_dir` adalah direktori tempat _file ZIP_ tersebut akan diekstrak setelah diunduh. `check_dir` adalah _path_ ke direktori yang akan digunakan untuk memeriksa apakah direktori sudah ada atau belum. `download_link` berisi _URL_ untuk mengunduh _file ZIP_ dari _Google Drive_. 

```c
if(access(check_dir, F_OK) == 0){
    printf("Folder unzip sudah ada.\n");
    return 0;
}
```
Potongan kode ini berfungsi untuk memeriksa apakah _folder_ hasil ekstrak (`check_dir`) sudah ada atau belum menggunakan fungsi `access()` dengan parameter `F_OK`. Jika _folder_ tersebut ditemukan atau sudah bisa diakses (`access()` mengembalikan 0), maka program mencetak pesan `"Folder unzip sudah ada."` dan langsung berhenti dengan `return 0`, yang menandakan bahwa program selesai dengan sukses tanpa perlu melakukan proses _download_ dan/atau ekstrak ulang.

```c
if(access(zip_file, F_OK) != 0){
    pid_t pid = fork();
    ...
}
```
Di sisi lain, jika _folder_ `check_dir` tidak bisa ditemukan atau diakses (`access() tidak mengambalikan 0`), maka program akan membuat proses baru menggunakan fungsi `fork()`. `fork()` akan menggandakan proses: satu untuk _parent_ dan satu untuk _child_. 

```c
if(pid == 0){
    execlp("wget", "wget", download_link, "-O", zip_file, NULL);
    perror("execlp gagal.\n");
    exit(1);
}
```
Jika `pid` yang dikembalikan oleh fungsi `fork()` bernilai 0 (`pid == 0`), maka bagian ini akan dijalankan oleh proses anak atau _child_. Di argumen pertama, akan dipanggil fungsi  `execlp()` yang digunakan untuk mengeksekusi perintah `wget` agar _file_ dari _URL_ yang diberikan dapat diunduh. Argumen kedua adalah nama perintah itu sendiri (`wget`). Argumen selanjutnya, `download_link`, adalah _URL_ dari _file_ yang akan diunduh dan argumen terakhir merupakan opsi `-O` yang digunakan untuk menentukan _output_ file, artinya hasil unduhan tidak akan disimpan dengan nama _default_ dari _URL_, tetapi langsung disimpan ke _path_ yang ditentukan oleh `zip_file`. Jika `execlp()` gagal dieksekusi, maka fungsi `perror()` akan mencetak pesan kesalahan, dan `exit(1)` digunakan untuk menghentikan proses anak dengan status `1`, yang menandakan adanya _error_.

```c
else if(pid > 0){
    int flag;
    waitpid(pid, &flag, 0);
                
    if(WIFEXITED(flag) && WEXITSTATUS(flag) == 0){
        printf("Download berhasil.\n");
    }
    else{
        printf("Download gagal.\n");
        return 1;
    }
}
```
Jika `pid` yang dikembalikan oleh fungsi `fork()` bernilai lebih dari 0 (`pid > 0`), maka bagian ini akan dijalankan oleh proses induk atau _parent_. Di awal, _parent_ menunggu proses anak selesai menggunakan `waitpid()`, yang akan menyimpan status keluar proses anak ke dalam variabel `flag`. Fungsi `WIFEXITED(flag)` memeriksa apakah proses anak telah keluar secara normal, dan `WEXITSTATUS(flag) == 0` memastikan bahwa proses keluar dengan status 0, artinya tidak ada _error_. Jika kedua kondisi terpenuhi, maka pesan `"Download berhasil."` akan ditampilkan. Namun jika tidak, maka dicetak `"Download gagal."` dan program dihentikan dengan `return 1` sebagai tanda adanya kegagalan.

```c
else{
    perror("fork gagal.\n");
    return 2;
}
```
Jika `pid` yang dikembalikan oleh fungsi `fork()` tidak bernilai 0 atau lebih dari 0 (`pid < 0`), maka _forking_ gagal dijalankan dan pesan kesalahan akan dicetak oleh fungsi `perror()`, kemudian program akan dihentikan dengan `return 2` sebagai tanda adanya kegagalan. (Nilai `return` yang berbeda hanya digunakan untuk memudahkan pemeriksaan kesalahan/_debugging_).

```c
pid_t pid = fork();
```
Setelah penggandaan proses atau _forking_ pertama selesai dengan tujuan melakukan mengunduh _file ZIP_ menggunakan perintah `wget`, bagian kode ini akan melakukan `forking` untuk yang kedua kalinya.

```c
if(pid == 0){
    execlp("unzip", "unzip", zip_file, "-d", unzip_dir, NULL);
    perror("execlp gagal.\n");
    exit(1);
}
```
Jika `pid` yang dikembalikan oleh fungsi `fork()` bernilai 0 (`pid == 0`), maka bagian ini akan dijalankan oleh proses anak atau _child_. Di argumen pertama, akan dipanggil fungsi  `execlp()` untuk mengeksekusi perintah `unzip` agar _file ZIP_ yang sudah diundah bisa diekstrak (_unzip_) ke direktori tujuan yang ditentukan dalam variabel `unzip_dir`. Argumen kedua adalah nama perintah itu sendiri (`unzip`). Argumen selanjutnya, `zip_file`, adalah _file ZIP_ yang akan diekstrak dan argumen terakhir merupakan opsi `-d` yang digunakan untuk menunjukkan direktori tujuan tempat _file_ yang akan diekstrak. Jika `execlp()` gagal dieksekusi, maka fungsi `perror()` akan mencetak pesan kesalahan, dan `exit(1)` digunakan untuk menghentikan proses anak dengan status `1`, yang menandakan adanya _error_.

```c
else if(pid > 0){
    int flag;
    waitpid(pid, &flag, 0);

    if(WIFEXITED(flag) && WEXITSTATUS(flag) == 0){
        printf("Unzip berhasil.\n");
    }
    else {
        printf("Unzip gagal.\n");
        return 3;
    }
}
```
Jika `pid` yang dikembalikan oleh fungsi `fork()` bernilai lebih dari 0 (`pid > 0`), maka bagian ini akan dijalankan oleh proses induk atau _parent_. Di awal, _parent_ menunggu proses anak selesai menggunakan `waitpid()`, yang akan menyimpan status keluar proses anak ke dalam variabel `flag`. Fungsi `WIFEXITED(flag)` memeriksa apakah proses anak telah keluar secara normal, dan `WEXITSTATUS(flag) == 0` memastikan bahwa proses keluar dengan status 0, artinya tidak ada _error_. Jika kedua kondisi terpenuhi, maka pesan `"Unzip berhasil."` akan ditampilkan. Namun jika tidak, maka dicetak `"Unzip gagal."` dan program dihentikan dengan `return 3` sebagai tanda adanya kegagalan.

```c
else{
    perror("fork gagal.\n");
    return 4;
}
```
Jika `pid` yang dikembalikan oleh fungsi `fork()` tidak bernilai 0 atau lebih dari 0 (`pid < 0`), maka _forking_ gagal dijalankan dan pesan kesalahan akan dicetak oleh fungsi `perror()`, kemudian program akan dihentikan dengan `return 4` sebagai tanda adanya kegagalan. 

```c
return 0;
```
Kode diakhiri oleh `return 0` yang menandakan bahwa program telah berhasil dieksekusi tanpa _error_.

### Foto Hasil Output

Sebelum:
![image alt](https://github.com/SuryaAndyartha/laporanmodul2/blob/main/Screenshot%202025-04-26%20at%2015.01.41.png?raw=true)

Eksekusi program:
![image alt](https://github.com/SuryaAndyartha/laporanmodul2/blob/main/Screenshot%202025-04-26%20at%2015.10.47.png?raw=true)
![image alt](https://github.com/SuryaAndyartha/laporanmodul2/blob/main/Screenshot%202025-04-26%20at%2015.11.09.png?raw=true)

Sesudah:
![image alt](https://github.com/SuryaAndyartha/laporanmodul2/blob/main/Screenshot%202025-04-26%20at%2015.03.08.png?raw=true)

b. trabowo-b.c; Code Lengkap:

```c
#include <stdio.h>
#include <stdlib.h>
#include <dirent.h>
#include <time.h>
#include <string.h>

int main(){
    char* dir_name = "/home/ubuntu/sisop_modul_2/resources/film";

    DIR *dir = opendir(dir_name);
    if(dir == NULL){
        printf("File unzip belum ada.\n");
        return 1;
    }

    char *film_array[100];
    int film_counter = 0;

    struct dirent* entity;
    entity = readdir(dir);
    while(entity != NULL){
        if(strstr(entity->d_name, ".jpg") != NULL){
            film_array[film_counter] = entity->d_name;
            film_counter++;
        }
        entity = readdir(dir);
    }

    closedir(dir);

    if(film_counter == 0){
        printf("Folder film kosong.\n");
        return 2;
    }

    srand(time(NULL));
    int index = rand() % film_counter;

    printf("Film for Trabowo & Peddy: '%s'\n", film_array[index]);

    return 0;
}
```

Penjelasan:

```c
#include <stdio.h>
#include <stdlib.h>
#include <dirent.h>
#include <time.h>
#include <string.h>
```
Kode dimulai oleh beberapa _library_ penting untuk menjalankan program sesuai yang diinginkan. `#include <stdio.h>` menyediakan akses ke fungsi `printf`. `#include <stdlib.h>` menyediakan akses ke fungsi `rand`. `#include <dirent.h>` menyediakan akses ke fungsi `opendir`, `readdir`, dan `closedir`. `#include <time.h>` menyediakan akses ke fungsi `time`. `#include <string.h>` menyediakan akses ke fungsi `strstr`.

```c
int main(){
    char* dir_name = "/home/ubuntu/sisop_modul_2/resources/film";
    ...
}
```
`int main(){...}` adalah fungsi utama yang menjadi titik masuk eksekusi program. Di dalamnya, program dimulai dan dieksekusi.
Setelahnya, terdapat bagian yang mendeklarasikan variabel-variabel sebagai _pointer_ dari tipe data `char`. `dir_name` menyimpan _path_ lengkap ke direktori yang akan diakses/dibuka untuk dilakukan pemeriksaan.

```c
DIR *dir = opendir(dir_name);
if(dir == NULL){
    printf("File unzip belum ada.\n");
    return 1;
}
```
Bagian kode ini digunakan untuk membuka direktori yang telah ditentukan dalam variabel `dir_name` menggunakan fungsi `opendir()`. `opendir()` mengembalikan pointer ke tipe data `DIR`. Jika `opendir()` gagal membuka direktori (misalnya karena direktori tidak ada atau tidak dapat diakses), maka nilai yang dikembalikan adalah `NULL`. Dalam kasus ini, program mencetak pesan `"File unzip belum ada."` menggunakan `printf()` dan menghentikan eksekusi program dengan `return 1` sebagai tanda adanya kegagalan.

```c
char *film_array[100];
int film_counter = 0;
```
Di sini, dilakukan deklarasi sebuah _array_ `film_array` yang berukuran `100` elemen dengan tipe data `char*`, yang masing-masing elemen akan menyimpan _pointer_ ke _string_. _Array_ ini digunakan untuk menyimpan nama-nama _file_ film yang ditemukan dalam direktori. Variabel `film_counter` diinisialisasi dengan nilai `0`, yang akan digunakan untuk menghitung jumlah _file_ film (dalam hal ini file dengan ekstensi .jpg).

```c
struct dirent* entity;
entity = readdir(dir);
```
Potongan kode ini mendeklarasikan sebuah _pointer_ `entity` bertipe `struct dirent`, yang digunakan untuk menyimpan informasi tentang entri dalam direktori. Kemudian, dilakukan pemanggilan fungsi `readdir()` untuk membaca entri pertama dalam direktori yang akan disimpan ke `entity`. Pemanggilan fungsi dilakukan oleh `entity = readdir(dir)`.

```c
 while(entity != NULL){
    if(strstr(entity->d_name, ".jpg") != NULL){
        film_array[film_counter] = entity->d_name;
        film_counter++;
    }
    entity = readdir(dir);
}
```
Pada bagian ini, terjadi perulangan atau _loop_ menggunakan `while()`. _Looping_ akan terus berjalan selama `entity` tidak bernilai `NULL` (masih ada entri di dalam direktori). Di dalam _loop_, fungsi `strstr()` digunakan untuk memeriksa apakah nama _file_ yang ada pada `entity->d_name` mengandung _substring_ `".jpg"`. Jika ditemukan, nama _file_ tersebut disimpan dalam _array_ `film_array` pada indeks `film_counter`, dan variabel `film_counter` akan bertambah 1 untuk menghitung jumlah _file_ yang ditemukan. Setelah itu, terjadi pemanggilan ulang fungsi `readdir()` oleh `entity = readdir(dir)` untuk memproses entri berikutnya. 

```c
closedir(dir);
```
Fungsi `closedir()` digunakan untuk menutup direktori yang sebelumnya dibuka `opendir()` sehingga tidak ada kebocoran memori atau masalah lain yang mungkin muncul. 
 
```c
if(film_counter == 0){
    printf("Folder film kosong.\n");
    return 2;
}
```
Jika nilai dari variabel `film_counter` ternyata 0 (`film_counter == 0`) setelah perulangan selesai, maka dapat disimpulkan bahwa direktori yang diakses kosong. Pesan kesalahan akan dicetak oleh fungsi `printf()`, kemudian program akan dihentikan dengan `return 2` sebagai tanda adanya kegagalan. (Nilai `return` yang berbeda hanya digunakan untuk memudahkan pemeriksaan kesalahan/_debugging_).

```c
srand(time(NULL));
int index = rand() % film_counter;
```
Pertama-tama, kode akan memanggil fungsi `srand()` yang digunakan untuk menginisialisasi generator angka acak dengan nilai yang dihasilkan dari waktu saat ini. Waktu saat ini bisa diperoleh melalui `time(NULL)` (fungsi ini akan mengembalikan jumlah detik yang telah berlalu sejak 1 Januari 1970 sehingga bisa dipastikan bahwa angka yang dihasilkan berbeda setiap kali program dijalankan). Kemudian, `int index = rand() % film_counter`; menghasilkan angka acak dalam rentang dari 0 hingga film_counter - 1 dengan menggunakan operator `modulo (%)`, dengan cara ini, bisa dipastikan bahwa rentang angka yang dihasilkan oleh fungsi `srand()` hanya berkisar di antara indeks jumlah _file_ pada direktori.

```c
printf("Film for Trabowo & Peddy: '%s'\n", film_array[index]);
```
Setelah sebuah _file_ dari direktori terpilih secara acak, maka akan dicetak menggunakan fungsi `printf()` sesuai dengan format yang telah ditentukan, yaitu `Film for Trabowo & Peddy: ‘<no_namafilm_genre.jpg>’`.

```c
return 0;
```
Kode diakhiri oleh `return 0` yang menandakan bahwa program telah berhasil dieksekusi tanpa _error_.

### Foto Hasil Output

![image alt](https://github.com/SuryaAndyartha/laporanmodul2/blob/main/Screenshot%202025-04-26%20at%2015.20.10.png?raw=true)

c. trabowo-c.c; Code Lengkap:
