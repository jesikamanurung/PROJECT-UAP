# PROJECT-UAP
PEMESANAN LAHAN PARKIR


#include <iostream>
#include <string>
#include <vector>
#include <list>
#include <stack>
#include <queue>
#include <algorithm> // Untuk std::sort

using namespace std;

// Kelas untuk mewakili pengguna
class User {
public:
    string nama;
    string kataSandi;

    User(const string& nama, const string& kataSandi) : nama(nama), kataSandi(kataSandi) {}
};

// Kelas untuk mewakili tempat parkir
class ParkingSpot {
public:
    int id;
    string lokasi;
    bool tersedia;

    ParkingSpot(int id, const string& lokasi) : id(id), lokasi(lokasi), tersedia(true) {}
};

// Kelas untuk mengelola sistem pemesanan parkir
class ParkingSystem {
private:
    vector<User> pengguna; // Array pengguna
    vector<ParkingSpot> tempatParkir; // Array tempat parkir
    stack<int> tempatParkirTersedia; // Stack untuk tempat parkir yang tersedia

    // Fungsi untuk mencari tempat parkir berdasarkan lokasi
    // Menggunakan Divide and Conquer
    vector<ParkingSpot> cariTempatParkir(const string& lokasi, int kiri, int kanan) {
        if (kiri > kanan) {
            return {}; // Tidak ditemukan
        }
        int tengah = (kiri + kanan) / 2;
        if (tempatParkir[tengah].lokasi == lokasi) {
            // Jika ditemukan
            vector<ParkingSpot> hasil;
            hasil.push_back(tempatParkir[tengah]); 
            return hasil;
        } else if (tempatParkir[tengah].lokasi > lokasi) {
            // Cari di sebelah kiri
            return cariTempatParkir(lokasi, kiri, tengah - 1); 
        } else {
            // Cari di sebelah kanan
            return cariTempatParkir(lokasi, tengah + 1, kanan); 
        }
    }

public:
    // Fungsi untuk mendaftarkan pengguna baru
    void daftarkanPengguna(const string& nama, const string& kataSandi) {
        pengguna.push_back(User(nama, kataSandi));
    }

    // Fungsi untuk memverifikasi login pengguna
    bool verifikasiLogin(const string& nama, const string& kataSandi) {
        for (const User& user : pengguna) {
            if (user.nama == nama && user.kataSandi == kataSandi) {
                return true;
            }
        }
        return false;
    }

    // Fungsi untuk menambahkan tempat parkir baru
    void tambahTempatParkir(int id, const string& lokasi) {
        tempatParkir.push_back(ParkingSpot(id, lokasi));
        tempatParkirTersedia.push(id); // Menambahkan ke stack
    }

    // Fungsi untuk menampilkan daftar tempat parkir
    void tampilkanDaftarTempatParkir() {
        cout << "Daftar Tempat Parkir:" << endl;
        for (const ParkingSpot& tempat : tempatParkir) {
            cout << "ID: " << tempat.id << ", Lokasi: " << tempat.lokasi << ", Tersedia: "
                 << (tempat.tersedia ? "Ya" : "Tidak") << endl;
        }
    }

    // Fungsi untuk mencari dan memesan tempat parkir
    void cariDanPesanTempatParkir(const string& lokasi, int& idTempatParkir) {
        // Mencari tempat parkir berdasarkan lokasi
        vector<ParkingSpot> hasilPencarian = cariTempatParkir(lokasi, 0, tempatParkir.size() - 1);

        if (hasilPencarian.empty()) {
            cout << "Tidak ditemukan tempat parkir di lokasi tersebut." << endl;
            return;
        }

        // Menampilkan pilihan tempat parkir yang tersedia
        cout << "Pilihan Tempat Parkir:" << endl;
        for (const ParkingSpot& tempat : hasilPencarian) {
            if (tempat.tersedia) {
                cout << "ID: " << tempat.id << ", Lokasi: " << tempat.lokasi << endl;
            }
        }

        // Meminta pengguna memilih tempat parkir
        cout << "Masukkan ID tempat parkir yang ingin dipesan: ";
        cin >> idTempatParkir;

        // Mencari tempat parkir yang dipilih
        for (ParkingSpot& tempat : tempatParkir) {
            if (tempat.id == idTempatParkir && tempat.tersedia) {
                tempat.tersedia = false; // Pesan tempat parkir
                tempatParkirTersedia.pop(); // Menghapus dari stack
                cout << "Tempat parkir berhasil dipesan!" << endl;
                return;
            }
        }

        cout << "Tempat parkir tidak tersedia atau ID tidak valid." << endl;
    }

    // Fungsi untuk menampilkan struk parkir
    void tampilkanStrukParkir(int idTempatParkir) {
        // Mencari tempat parkir berdasarkan ID
        for (const ParkingSpot& tempat : tempatParkir) {
            if (tempat.id == idTempatParkir) {
                cout << "----- STRUK PARKIR -----" << endl;
                cout << "ID Tempat Parkir: " << tempat.id << endl;
                cout << "Lokasi: " << tempat.lokasi << endl;
                // ... Tambahkan informasi lain pada struk parkir ...
                cout << "-------------------------" << endl;
                return;
            }
        }
        cout << "Tempat parkir dengan ID " << idTempatParkir << " tidak ditemukan." << endl;
    }
};

int main() {
    ParkingSystem sistemParkir;

    // Inisialisasi tempat parkir
    sistemParkir.tambahTempatParkir(1, "Gedung A");
    sistemParkir.tambahTempatParkir(2, "Gedung A");
    sistemParkir.tambahTempatParkir(3, "Gedung B");
    sistemParkir.tambahTempatParkir(4, "Gedung C");

    // Menu utama
    int pilihan;
    do {
        cout << "\n--- Sistem Pemesanan Parkir ---" << endl;
        cout << "1. Daftar Pengguna" << endl;
        cout << "2. Login Pengguna" << endl;
        cout << "3. Tampilkan Daftar Tempat Parkir" << endl;
        cout << "4. Cari dan Pesan Tempat Parkir" << endl;
        cout << "5. Tampilkan Struk Parkir" << endl;
        cout << "0. Keluar" << endl;
        cout << "Pilih menu: ";
        cin >> pilihan;

        switch (pilihan) {
            case 1: {
                string nama, kataSandi;
                cout << "Masukkan nama pengguna: ";
                cin >> nama;
                cout << "Masukkan kata sandi: ";
                cin >> kataSandi;
                sistemParkir.daftarkanPengguna(nama, kataSandi);
                cout << "Pendaftaran berhasil!" << endl;
                break;
            }
            case 2: {
                string nama, kataSandi;
                cout << "Masukkan nama pengguna: ";
                cin >> nama;
                cout << "Masukkan kata sandi: ";
                cin >> kataSandi;
                if (sistemParkir.verifikasiLogin(nama, kataSandi)) {
                    cout << "Login berhasil!" << endl;
                    // ... Lanjutkan ke menu utama sistem ...
                } else {
                    cout << "Nama pengguna atau kata sandi salah." << endl;
                }
                break;
            }
            case 3:
                sistemParkir.tampilkanDaftarTempatParkir();
                break;
            case 4: {
                string lokasi;
                int idTempatParkir;
                cout << "Masukkan lokasi tempat parkir: ";
                cin >> lokasi;
                sistemParkir.cariDanPesanTempatParkir(lokasi, idTempatParkir);
                break;
            }
            case 5: {
                int idTempatParkir;
                cout << "Masukkan ID tempat parkir: ";
                cin >> idTempatParkir;
                sistemParkir.tampilkanStrukParkir(idTempatParkir);
                break;
            }
            case 0:
                cout << "Terima kasih! Keluar dari sistem." << endl;
                break;
            default:
                cout << "Pilihan tidak valid." << endl;
                break;
        }
    } while (pilihan != 0);

    return 0;
}
