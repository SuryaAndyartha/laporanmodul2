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
Kode dimulai oleh beberapa _library_ penting untuk menjalankan program sesuai yang diinginkan. ```#include <stdio.h>``` menyediakan akses ke fungsi ```printf```. ```#include <unistd.h>``` memberikan akses ke fungsi ```fork```, ```execlp```, dan ```access```. ```#include <sys/wait.h>``` menyediakan fungsi ```waitpid```, ```WIFEXITED```, dan ```WEXITSTATUS```. ```#include <stdlib.h>``` menyediakan fungsi ```exit``` dan ```perror```.

```c
int main(){
    char *zip_file = "/home/ubuntu/sisop_modul_2/resources/film.zip";
    char *unzip_dir = "/home/ubuntu/sisop_modul_2/resources";
    char *check_dir = "/home/ubuntu/sisop_modul_2/resources/film";
    char *download_link = "https://drive.google.com/uc?export=download&id=1nP5kjCi9ReDk5ILgnM7UCnrQwFH67Z9B";
    ...
}
```
```int main(){...}``` adalah fungsi utama yang menjadi titik masuk eksekusi program. Di dalamnya, program dimulai dan dieksekusi.
Setelahnya, terdapat bagian yang mendeklarasikan variabel-variabel sebagai _pointer_ dari tipe data ```char```. ```zip_file``` menyimpan _path_ lengkap ke _file ZIP_ yang akan diunduh, yaitu ```film.zip```. ```unzip_dir``` adalah direktori tempat _file ZIP_ tersebut akan diekstrak setelah diunduh. ```check_dir``` adalah _path_ ke direktori yang akan digunakan untuk memeriksa apakah direktori sudah ada atau belum. ```download_link``` berisi _URL_ untuk mengunduh _file ZIP_ dari _Google Drive_. 

```c
if(access(check_dir, F_OK) == 0){
    printf("Folder unzip sudah ada.\n");
    return 0;
}
```
```c
if(access(zip_file, F_OK) != 0){
    pid_t pid = fork();
    ...
}
```
```c
if(pid == 0){
    execlp("wget", "wget", download_link, "-O", zip_file, NULL);
    perror("execlp gagal.\n");
    exit(1);
}
```
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
```c
else{
    perror("fork gagal.\n");
    return 2;
}
```
```c
pid_t pid = fork();
```
```c
if(pid == 0){
    execlp("unzip", "unzip", zip_file, "-d", unzip_dir, NULL);
    perror("execlp gagal.\n");
    exit(1);
}
```
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
```c
else{
    perror("fork gagal.\n");
    return 4;
}
```
```c
return 0;
```
### Foto Hasil Output

![image alt]()

b. trabowo-b.c; Code Lengkap:
